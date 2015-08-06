
Command Line Tutorials &#8211; Sed &#038; Awk

  <div class="post_body">
    <p>Hey everybody, welcome back to my ongoing series on mastering your command line. It&#39;s time to  command and conquer! As always, make sure you check out some of the <a href="http://quickleft.com/blog/command-line-tutorials-tips-tricks">other posts</a> in the series.</p>

<p>This week we&#39;re going to take a look at two very important commands for your toolbox &#8211; &quot;sed&quot; and &quot;awk&quot;. These two commands are pretty powerful, but as a result, their learning curve can be pretty steep. I&#39;ll provide you an outline to get you up to speed on sed and awk, but there&#39;s more to these commands than I have room here to cover. Fortunately, there&#39;s a ton of information on the internet about these commands, and I encourage you to read up on them once we&#39;re done.</p>

<p>The first command we&#39;ll discuss is &quot;sed&quot;, which stands for &quot;stream editor&quot; &#8211; it gives you a nifty way to perform operations on files you&#39;re passing around through pipes. If that sounds confusing, hang on for one second &#8211; an example should help clear it up. One of the most common things you&#39;ll use sed for is text substitution, i.e. replacing certain text with something else. The syntax for doing that looks like this:</p>

<p><script src="https://gist.github.com/3298713.js"> </script></p>

<p>First we perform an echo on a string, so we have some data to actually work with. That text gets piped to the &quot;sed&quot; command (pipes are super important for sed and awk; if you need a refresher, check out <a href="http://quickleft.com/blog/command-line-tutorials-redirection-pipes">my post on redirection and pipes</a>). Let&#39;s break apart that &quot;sed&quot; command.</p>

<p>First, we have &quot;s&quot;, for substitute. Next is &quot;/&quot;, our delimiter for separating the different components of the command (note that you&#39;re not limited to just &quot;/&quot;; if what you&#39;re searching for contains slashes, it might be more convenient to use an underscore or a colon). After the first delimiter, we have a <a href="http://en.wikipedia.org/wiki/Regular_expression">regular expression</a> for the text we&#39;d like to replace. Finally, we have another delimiter, followed by the replacement string (i.e. what you&#39;d like the old text to say after running the command), with a trailing delimiter after that. Phew, that&#39;s lengthy!</p>

<p><img src="/wp-content/uploads/main_15b812a1-526e-45fb-b7fc-d5972d1c8492.jpeg" alt=""></p>

<p>Try out that command, and you should see &quot;it&#39;s a tarp&quot; printed out. Substitution on a basic string isn&#39;t too useful, so let&#39;s try it out on a file instead. Type the following two commands:</p>

<p><script src="https://gist.github.com/3298716.js"> </script></p>

<p>First we create the file, then we perform the substition. You&#39;ll see the result fed to the screen, but you should see only the first &quot;ow&quot; got replaced. Why? Only the first match of the regex got replaced. To hit every match, you need to add a flag to the end of the &quot;sed&quot; command, like so:</p>

<p><script src="https://gist.github.com/3298718.js"> </script></p>

<p>That&#39;s more like it, but if you check out the file, you&#39;ll see that that change wasn&#39;t actually saved. You could pipe the output to the same file, but &quot;sed&quot; provides another flag that will do it for you. Try this:</p>

<p><script src="https://gist.github.com/3298721.js"> </script></p>

<p>There we go! What else can &quot;sed&quot; do? Deletions are pretty simple. Here&#39;s an example that duplicates the functionality of the &quot;head&quot; command.</p>

<p><script src="https://gist.github.com/3298723.js"> </script></p>

<p>Of course, this doesn&#39;t actually delete the lines; you&#39;re not writing to the file, so it just feeds those lines out to your shell. The &quot;11,$&quot; portion of the command tells &quot;sed&quot; to delete everything from the eleventh line to the end of the file. You&#39;re not limited to numbers for that range; instead you can use regular expressions to mark the beginning and end of what you&#39;d like to delete. Or, if you&#39;d like, you can use one expression, causing &quot;sed&quot; to delete everything that matches it. You can use sed to delete comments from a Ruby script like so:</p>

<p><script src="https://gist.github.com/3298725.js"> </script></p>

<p>That regular expression matches every comment in a file &#8211; any text that follows a &quot;#&quot;.</p>

<p>There&#39;s a bunch more to &quot;sed&quot;, but let&#39;s move onto &quot;awk&quot; now. &quot;awk&quot; is a utility that&#39;s useful for processing text files. The utility considers each file as a set of records, which by default are the lines in the file. &quot;awk&quot; enables you to create a condition and action pair, and for each record that matches the condition, the action will fire. That&#39;s really wordy, so hopefully an example will help. First, let&#39;s create a file to use for the demo, then let&#39;s run a basic &quot;awk&quot; command on that file.</p>

<p><script src="https://gist.github.com/3308256.js"> </script></p>

<p>Again, let&#39;s break it down. Most &quot;awk&quot; commands follow this pattern &#8211; you have a condition, followed by an action in curly braces. Our condition in this case is the regular expression /root/, which will match any line that contains &quot;root&quot;. The action that follows is written in the AWK language, which could take up an entire series of blog posts, but all you need to know here is that it&#39;s printing the first and ninth fields (&quot;columns&quot;) of the record (which in our case are the permissions of the file and the filename). You can play around with the expression and see how many matches you can get, and you can also add columns to the action statement. You can also omit the action entirely, like so:</p>

<p><script src="https://gist.github.com/3298730.js"> </script></p>

<p>The default action for &quot;awk&quot; is to print the entire record, so the previous command will print every line that contains &quot;root&quot;. Of course, you can use other conditions; for example, checking whether or not a certain field is above or below a given threshold (i.e. $5 &gt; 2), or checking whether a record has a certain column. Your actions can be varied too, based on the AWK language. You can use &quot;awk&quot; to substitute just like &quot;sed&quot;, by using the AWK &quot;sub&quot; or &quot;gsub&quot; commands. Here, I&#39;ll change every occurence of &quot;jessica&quot; to &quot;nicelady&quot;, but only if the filename starts with a period:</p>

<p><script src="https://gist.github.com/3298735.js"> </script></p>

<p>Wow, that&#39;s a beefy command. First, the condition checks whether or not $9, the ninth field, matches the regular expression /^./; in plain people language, does the filename start with a period? Then the action calls the AWK function gsub, for global substitution. Everywhere in the file that matches /jessica/, we substitute the string &quot;nicelady&quot;. Then, once that&#39;s done, we print the line. If I want to make that change to the file permanent, then the easiest way is to pipe the output straight to the file, adding &quot; | ~/temp.txt&quot; onto the end of the &quot;awk&quot; command.</p>

<p>So for a quick overview, &quot;sed&quot; and &quot;awk&quot; are flexible stream editing utilities. They operate a little differently, but each relies on the power of regular expressions and text matching to do its job. There is SO MUCH MORE to these commands that I hope you take the time to look them up; hopefully this introduction opened your eyes to the world of command line stream editing.</p>

<p>That&#39;s all for this week! Post in the comments if you have anything to add, or any questions to ask!</p>
