---
layout: post
title: Custom User Authentication Provider in Symfony2
date: 12th September 2012
excerpt: How to authenticate users in your Symfony 2.1 application, against an external (API) service
---

This article is for Symfony 2.1, but not much is different here for 2.0,
 and the upgrade docs are very good so should cover the changes required if you are still working with 2.0.

##What are we doing?
Within a Symfony application, it's very simple to authenticate your users
 against anything you need, be it LDAP, A Database within your application, 
 or an external service.

I'm using 'external' to mean anything outside of the application,
 I have the need to authenticate users against an external ACL API, which is
 actually internal to our company systems.

##First things first.

I'm making an assumption here that you have already been through the Symfony
documentation in detail. It provides some excellent information on the security
layer, and is certainly a pre requisite of understanding some of the underlying details.

If you haven't, then I'll wait while you go and read it now.

[http://symfony.com/doc/current/book/security.html](http://symfony.com/doc/current/book/security.html) and
 [http://symfony.com/doc/current/cookbook/security/index.html](http://symfony.com/doc/current/cookbook/security/index.html) for starters

##So in short, the question is;

How do I authenticate users against this API, without doing the passwords comparison locally.
In this case, you need to pass the username and password to the API, and have
 it tell you if authentication succeeds or not.

I'm going to assume you have already implemented a class that acts as a Client
 for your API service, and potentially container for returned values.
It may have methods like authenticate(), getFirstName() etc.
And it's up to you to work out how often this needs to really be a call to
 the API, maybe implement caching in your client class?


##To work!

There isn't actually much to this, the security layer in Symfony2 really does
 make extending and replacing parts quite easy.
 I'm keeping the code here fairly complete to ease the understanding, but,
 as always, you should try to understand what is happening under the hood,
 rather than copy/pasting code into your application.
 This is even more important when it comes to aspects likes security.

###UserAuthenticationProvider

Let's start with the our UserAuthenticationProvider, which holds the implementation
 to actually perform the auth check against the API.

{% highlight php %}
<?php

namespace Catchamonkey\Bundle\UserBundle\Security\Authentication\Provider;

use Catchamonkey\Bundle\UserBundle\Security\User\AclApiUser;
use Catchamonkey\Component\AclApiClient\Client as AclApiClient;

use Symfony\Component\Security\Core\Authentication\Provider\UserAuthenticationProvider;
use Symfony\Component\Security\Core\Authentication\Token\UsernamePasswordToken;
use Symfony\Component\Security\Core\Exception\AuthenticationException;
use Symfony\Component\Security\Core\User\UserInterface;

class AclApiUserAuthenticationProvider extends UserAuthenticationProvider
{
    private $_aclApiClient;

    public function setAclApiClient(AclApiClient $aclApiClient)
    {
        $this->_aclApiClient = $aclApiClient;
    }

    /**
     * Retrieves the user if authentication succeeds via the API call
     * {@inheritdoc}
     */
    protected function retrieveUser($username, UsernamePasswordToken $token)
    {
        $user = $token->getUser();
        if ($user instanceof UserInterface) {
            return $user;
        }

        if ($this->_aclApiClient->authenticate($username, $token->getCredentials())) {

            return new AclApiUser(
                $username,
                $this->_aclApiClient->getFirstName(),
                $this->_aclApiClient->getLastName(),
                $this->_aclApiClient->getId()
            );
        }

        throw new AuthenticationException('Username invalid or password incorrect.');
    }

    /**
     * No authentication check here, it is done during retrieveUser
     */
    protected function checkAuthentication(UserInterface $user, UsernamePasswordToken $token) {}
}
?>
{% endhighlight %}

As you can see, when retrieveUser user is called, it gets passed the username,
 and the token, if the token already contains an instance of the UserInterface,
 then we can just return it as authentication has already occurred.
 Otherwise, we ask the API to authenticate using the details provided.
 If that works, we create a new AclApiUser using the returned details.
And if it all fails, we throw the AuthenticationException.

###So, what is in the AclApiUser class?

{% highlight php %}
<?php

namespace Catchamonkey\Bundle\UserBundle\Security\User;

use Symfony\Component\Security\Core\User\UserInterface;
use Symfony\Component\Security\Core\User\EquatableInterface;

class AclApiUser implements UserInterface, EquatableInterface
{
    protected $username;
    protected $firstName;
    protected $lastName;
    protected $aclUserId;
    protected $roles;

    public function __construct($username, $firstName, $lastName, $aclUserId, array $roles = array())
    {
        $this->username     = $username;
        $this->firstName    = $firstName;
        $this->lastName     = $lastName;
        $this->aclUserId    = $aclUserId;
        $this->roles        = $roles;
    }

    /**
     * {@inheritdoc}
     */
    public function getRoles()
    {
        return $this->roles;
    }

    /**
     * {@inheritdoc}
     */
    public function getPassword() {}

    /**
     * {@inheritdoc}
     */
    public function getSalt() {}

    /**
     * {@inheritdoc}
     */
    public function getUsername()
    {
        return $this->username;
    }

    public function getFirstName()
    {
        return $this->firstName;
    }

    public function getLastName()
    {
        return $this->lastName;
    }

    public function getAclUserId()
    {
        return $this->aclUserId;
    }

    /**
     * {@inheritdoc}
     */
    public function eraseCredentials() {}

    /**
     * {@inheritdoc}
     */
    public function isEqualTo(UserInterface $user)
    {
        if (
            $user instanceof AclApiUser &&
            $this->username == $user->getUsername() &&
            $this->firstName == $user->getFirstName() &&
            $this->lastName == $user->getLastName() &&
            $this->roles == $user->getRoles()
        ) {
            return true;
        }

        // otherwise we want it to be refreshed
        return false;
    }
}
?>
{% endhighlight %}

This is a pretty standard implementation, other than storing the userId that the
 ACL API returns us if authentication succeeds.

###AclApiUserProvider

{% highlight php %}
<?php

namespace Catchamonkey\Bundle\UserBundle\Security\User;

use Symfony\Component\Security\Core\User\UserInterface;
use Symfony\Component\Security\Core\User\UserProviderInterface;
use Symfony\Component\Security\Core\Exception\UsernameNotFoundException;
use Symfony\Component\Security\Core\Exception\UnsupportedUserException;

use Catchamonkey\Bundle\UserBundle\Security\User\AclApiUser;

/**
 * 
 * {@inheritdoc}
 */
class AclApiUserProvider implements UserProviderInterface
{
    private $_aclApiClient;

    public function __construct()
    {
        $this->_aclApiClient = NULL;
    }

    public function setAclApiClient($aclApiClient)
    {
        $this->_aclApiClient = $aclApiClient;
    }

    /**
     * {@inheritdoc}
     */
    public function loadUserByUsername($username)
    {
        if ($this->_aclApiClient->loadByUsername($username)) {

            return new AclApiUser(
                $this->_aclApiClient->getUsername(),
                $this->_aclApiClient->getFirstName(),
                $this->_aclApiClient->getLastName(),
                $this->_aclApiClient->getId(),
                $this->_aclApiClient->getRoles()
            );
        }

        throw new UsernameNotFoundException(sprintf('Username "%s" does not exist.', $username));
    }

    /**
     * {@inheritdoc}
     * refreshUser is called on each request, a comparison is then done using the 
     * isEqualTo method in your UserInterface class against the current instance
     */
    public function refreshUser(UserInterface $user)
    {
        if ($this->supportsClass(get_class($user))) {

            return $this->loadUserByUsername($user->getUsername());
        }

        throw new UnsupportedUserException();
    }

    public function supportsClass($class)
    {
        return 'Catchamonkey\\Bundle\\UserBundle\\Security\\User\\AclApiUser' === $class;
    }
}
?>
{% endhighlight %}

###Services

Let's wire up our services

{% highlight yaml %}
parameters:
    catchamonkey_user.user_authentication_provider.class:   Catchamonkey\Bundle\UserBundle\Security\Authentication\Provider\AclApiUserAuthenticationProvider
    catchamonkey_user.user_provider.class:                  Catchamonkey\Bundle\UserBundle\Security\User\AclApiUserProvider

services:
    catchamonkey_user.user_authentication_provider:
        class: %catchamonkey_user.user_authentication_provider.class%
        arguments:  [@security.user_checker, '', %security.authentication.hide_user_not_found%]
        calls:
            - [ setAclApiClient,  [@catchamonkey_user.acl_api_client] ]

    catchamonkey_user.user_provider:
        class: %catchamonkey_user.user_provider.class%
        calls:
            - [ setAclApiClient, [@catchamonkey_user.acl_api_client] ]

    catchamonkey.acl_api_client:
        class: %catchamonkey.acl_api_client.class%
        calls:
            - [ setAclApiUrl, [%catchamonkey_user.acl_api_uri%] ]
{% endhighlight %}