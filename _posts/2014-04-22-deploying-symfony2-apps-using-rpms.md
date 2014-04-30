---
layout: post
title: Deploying Symfony2 apps using RPMs
date: 22nd April 2014
excerpt: A guide and .spec file example on deployment of Symfony2 apps using RPMs
---

##Note

This does assume at least a basic undersatnding of RPMs and spec files, but all of the Symfony2 specific work is outlined here for your enjoyment. If you would like to know more about RPMs and .spec files, then [Maximum RPM](http://www.rpm.org/max-rpm/ "Maximum RPM") is a good resource.

##tl;dr
The [spec file can be found over at GitHub gist](https://gist.github.com/catchamonkey/8575619f450fe4c94acd).
You will of course need to update the top section realting to name, and requirements etc, the ones I have left in there are real world though so should give you a good prompt to get going.

##The .spec file
I'll explain the sections one by one below, the steps that happen prior to those detailed below are simply pulling down the main repo source.

To achieve this, we use a homegrown script that pulls from a Git repo, to a specific tagged version.

###%prep

This section is pretty straight forward, we use composer to pull down the dependencies of the repo, with the usual flags, but we also disable the scripts at this point.

{% highlight bash %}
composer install --prefer-dist -o -n --no-dev --no-progress --no-scripts
{% endhighlight %}

###%build

####Run the post-install-cmd scripts (cache clear, install assets etc)

{% highlight bash %}
composer run-script post-install-cmd --no-dev --no-interaction
{% endhighlight %}

####Update the version used in the asset output scheme

{% highlight bash %}
sed -i -e 's/\(assets_version: v=[ ]*\)\([a-zA-Z0-9_]*\)\(.*\)$/\1%{version}\3/g' app/config/config.yml
{% endhighlight %}

####Dump the assets (not done via any registered ScriptHandler)

{% highlight bash %}
app/console assetic:dump --env=prod
{% endhighlight %}

####Remove some files not needed in production

Apart from the removal of non-production controllers (```rm web/app_*.php```), you could skip this part.

The parameters.yml file is needed in order to warm the cache and run the post-install-cmd scripts, but the real one is centrally managed on our servers via puppet so we remove it here, you may be managing your config files differently so you may need to mofidy this part.

{% highlight bash %}
rm web/app_*.php composer.* behat.yml phpspec.yml .travis.yml app/config/parameters.yml
{% endhighlight %}

####Move the generated cache into a non-standard location

This is one of the most important things you must do when deploying an application that has cached data relating to the application within it.

When you install an RPM, the new version of code is put in place, but old files that are not in the new version are only removed if you use the %files section to list all files individually. That isn't really feasible when the files are generated and the names change between runs (twig template cache etc).

This gets around that issue by putting these files into a cache/build directory, and after install (more later), mv'ing them over the cache/prod directory, which is a full OS level dir replace, and is also atomic, this means that you have an (almost) full cache put in place at the right moment, rather than generating the cache in place on your production servers.

{% highlight bash %}
mv app/cache/prod app/cache/build
{% endhighlight %}

####Modify the paths written in the cache files

When Symfony warms the cache, it uses the current directory as the basis for the cache directory. This is of course a problem when you are warming as we are here in a different directory.

We therefore use the package name, and the version being built (which is part of the current dir) and modify all the files in app/cache to have the directory that will be used once installed.

(I'm delimiting with a # for readability within path replacement.)

The directory during build for us is ```/home/rpm.private/BUILD/%{name}-%{version}```, and the directory we install into on production is ```/home/sites/%{name}```

Find all the files in cache, then edit them using sed.

{% highlight bash %}
find app/cache/ -type f | xargs sed -i "s#%{_builddir}/%{name}-%{version}#/home/sites/%{name}#ig"
{% endhighlight %}


###%install

This is a standard install section, nothing unique needs to happen here. We install the vhost and then copy the files into place


###%post

Here we do something a bit different.

If this is the first installation of this package, then we change ownership of the cache directory to apache as there are still some cache files that are generated during the first request.
We then move the cache/build directory into it's rightful place in cache/prod

{% highlight bash %}
if [ $1 -eq 1 ]; then
    # Grant apache access to it so remaining items can be cached (doctrine annotations)
    chown -R apache:apache /home/sites/%{name}/app/cache/build

    # move the built cache into place
    mv /home/sites/%{name}/app/cache/build /home/sites/%{name}/app/cache/prod
fi
{% endhighlight %}

<strong>Note:</strong> I am looking at removing this writable requirement by warming the rest using the built in PHP web server, and curl during %build. I have already made the code compatible, with this PR on doctrine/annotations. https://github.com/doctrine/annotations/pull/30

###%triggerpostun

We do the same as in %post here, but the triggerpostun is only run after a successful uninstall (when you upgrade the old package is uninstalled).

Only moved the created cache into place if at least 1 version will be left behind from the uninstall

$2 is the number of versions left after an uninstall of %{name} has been performed

```>=1``` means this is an upgrade, anything smaller means a complete removal

{% highlight bash %}
if [ $2 -ge 1 ]; then
    chown -R apache:apache /home/sites/%{name}/app/cache/build
    mv /home/sites/%{name}/app/cache/build /home/sites/%{name}/app/cache/prod
fi
{% endhighlight %}


###%post and %triggerpostun

We have the checks in these sections for number of versions for a very good reason. When you are upgrading, you only want to move the cache in place once the old version has been uninstalled. That way you can be sure that requests coming in are hitting new code, and new cache. Otherwise you may have a request hit old code and new cache or vice versa, either way, bad things happen for your users, and your error logs.

##%clean, %files and more

I've left out the other parts of the .spec file here as they are pretty much standard, but [the full file is on GitHub gist](https://gist.github.com/catchamonkey/8575619f450fe4c94acd) so you can check it out.


##Questions?

If you have any questions, then i'd be happy to try and answer them, you can find me in most places as catchamonkey, [GitHub](https://github.com/catchamonkey), [Twitter](https://twitter.com/catchamonkey), [Sensio Connect](https://connect.sensiolabs.com/profile/catchamonkey) etc, or just ask in the comments.
