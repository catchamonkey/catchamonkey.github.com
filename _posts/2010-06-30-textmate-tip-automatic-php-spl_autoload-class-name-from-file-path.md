---
layout: post
title: Textmate tip, Automatic PHP spl_autoload class name from file path
date: 30th June 2010
excerpt: We can use the Textmate environment variables, along with a small bit of PHP to automatically insert class names based on the folder directory structure
---
The title says it all so i'll just get on with it.  

Create a new textmate command, and set the output to 'Insert as snippet'  
Paste in the following.
{% highlight php %}
#!/usr/bin/php
<?php
$path = $_ENV['TM_FILEPATH'];
$path = trim($path, '/');
$path = trim($path, '.php');
$parts = explode('/', $path);
$lastPart = end($parts);
echo 'class ';
foreach ($parts as $id => $part) {
    // textmate placeholders start at 1
    $id = $id+1;
    if ($lastPart == $part) {
        echo '${'.$id.':'.$part.'}';
    } else {
        echo '${'.$id.':'.$part.'_}';
    }
}
?>
{% endhighlight %}

And bind to a tab trigger.  
When you execute from within a file it performs the following.

Retrieves the full path of the file you are in.  
Removes any forward slashes from the beginning and end of the path.  
Removes the .php extension from the end of the file.  
Explodes each part of the path, seperated by a slash (/) into an array.  
Gets the pointer for the last item in the array.  
echo's 'class'  
Then iterates through the array, creating a textmate snippet placeholder for each one, all of which have the required underscore (_) apart from the last one.  

So for a file that is in the path /The/Full/Path/Filename.php  
We end up with the resulting snippet of  
{% highlight php %}
${1:The_}${2:Full_}${3:Path_}${4:Filename}
{% endhighlight %}

Which then enables you to tab through them and remove those from the start that you don't need.  
You could go a step further and only spit out the last 3 parts as that is generally how many are needed for a class name. But it really depends on your app structure so I've left it as is for now.  

Check out the image for the options  
[![Textmate Command Options](/images/textmateCommand-300x205.jpg)](/images/textmateCommand.jpg)

###Update:
I've made a version that gives you just the last 3 parts of the path as mentioned above.  
I have both running on the 'class' tab command so can choose between them as required.

{% highlight php %}
#!/usr/bin/php
<?php
$path = $_ENV['TM_FILEPATH'];
$path = trim($path, '/');
$path = trim($path, '.php');
$parts = explode('/', $path);
echo 'class ';
// do this 3 times
for($i = 1; $i <= 3; $i++) {
  $varName = 'part_'.$i;
  $part = array_pop($parts);
  if ($i == 1) {
    $$varName = '${'.$i.':'.$part.'}';
  } else {
    $$varName = '${'.$i.':'.$part.'_}';
  }
}
// pass the snippet to the file
echo $part_3.$part_2.$part_1;
?>
{% endhighlight %}