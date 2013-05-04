#Questions from Steven#
Date: 5.2.2013
---------------------

###1.)###
>is there a way to simply link a cms page to the Exploded Menu? All the "hacks" I've found in the 
>forum do not work.  We need to do this to respect the URL conventions we've already established. For 
>instance `http://www.brick-anew.com/brick-anew-policies.html` on our x-cart site would be 
>`http://magento.brick-anew.com/index.php/articles-home-improvement/brick-anew-policies.html` 
>if added to the exploded menu on the new site, since the `articles-home-improvement` portion 
>would be inherited from it's parent category "Resources" in the nav. Thus, we would lose 
>traffic to the original link. 

There are multiple ways to do this. To accomplish what you are asking you need a `301 redirect` that would go within the `.htaccess` config file in the root directory of the new Magento site (assuming the current server is run off Apache). About 80% of servers are Apache but note that this solution is different if the server is a Microsoft IIS based system, CentOS based, etc.

Google's Answer:
    
    http://support.google.com/webmasters/bin/answer.py?hl=en&answer=93633

You will have to redirect your old URL to your new one to prevent links breaking. Mistakes in this area can be costly and can ultimately result in search engine results breaking, external site links pointing inward breaking, and ultimately the entire SEO reputation of the site being lost.

**.htaccess 301 redirect**

An example redirect:

    RewriteEngine on
    RewriteCond %{HTTP_HOST} ^www\.brick-anew\.com$
    RewriteRule ^(.*)$ http://www.magento.brick-anew.com [R=301,L]

That example would redirect all `www.brick-anew.com` requests to `www.magento.brick-anew.com`. After writing the redirect your link in the "exploded menu" can be to the old URL or to the new URL since the former is being forwarded to the latter. 

Finally, be sure to hide `index.php` in the url using these redirects. The `index.php` portion of the URL is negative because it serves no purpose in describing the link, making it unnecessarily complicated and more likely to be truncated in search results. 

Showing any `.php` filenames in this way allows an attacker to more readily profile a target for vulnerabilities and ways to break past site security. Because I know just from the URL that the site uses PHP, I automatically know that it may be vulnerable to SQL injection or XSS scripting attacks. A file extension of `.php` is also debated in SEO circles because it is an immediate red flag that a site is being served from a database, thus more likely to be spam.

**Writing Relative Links**

One reason you are encountering this problem is because most of the site was built using absolute link paths because of SEO superstition about link paths in years past. You can avoid this in the future if you be sure to use relative link paths rather than absolute paths for all links, image sources and external code sources, like JavaScript files.

Proper relative link:

    ../fireplace-doors/my-fireplace-doors.html

In this example we first move up one directory from the directory of our current page, then move down the directory into `/fireplace-doors/` in order to point to the `my-fireplace-doors.html` page.

Bad absolute link:

    http://www.brick-anew.com/fireplace-doors/my-fireplace-doors.html

Here the link is hard coded with a path and can cause numerous problems. If the base domain were changed these would break and have to be redirected as is currently the case. Worse, if someone is on the SSL version of the page with the protocol `https://` this link would take them away from their secure session and dump them on an unsecure `http://` page against their will. 

Protocols become especially important when linking `src` paths for images and external files. Say we use this script call:

    <script type='text/javascript' src='http://www.brick-anew.com/myJS.js'></script>

When a user reaches a secure `https` page the browser will throw an insecure session error because non-secure content is being served into an area intended to be secure, such as a checkout page. For this reason it is extremely important to be `protocol agnostic`, meaning that you call files with the current protocol of a page, whatever that may be.

Protocol agnostic version:

    <script type='text/javascript' src='//www.brick-anew.com/myJS.js'></script>

Setting up all images and JS in this way will cure insecure page errors in the vast majority of situations where you may be seeing that particular error.


###2.)###
>If not, can we get rid of the blue attribute column that generates on every "Category/ CMS Block" 
>page? The column that helps refine search based on attributes (e.g., "Compare Products")
>
>Second topic: I commented out the `ultcustomernav.phtml` page in order to get rid of the "My Cart and 
>Account" section from the Nav Menu visually. 

I'm not yet familiar with the layout but what you are describing sounds like a poor solution to achieve what you are trying to accomplish. If you comment code out but still leave the lines in place  then that code still has to be parsed and acted upon by the browser. 

Although nothing comes through on the front end, browser overhead is wasted having to read through comments which in turn slows the site speed. No comments should ever be displayed on a live production site, all should be stripped via a build file to ensure any code commenting is restricted to your development environment.

Say we want to get rid of a particular div with id `myDiv` but we can't remove the lines for whatever reason. Rather than commenting the lines out, the element should be removed from the DOM completely by JavaScript at runtime to get it off the page, a process known as `duck punching`:

Remove using jQuery after DOM ready:
    
    $(function(){
    	$('#myDiv').remove();
    });

Or if not necessary to wait on DOM ready the function can be immediately invoked:

    (function(){
        $('#myDiv').remove();
    }());



###3.)###
>We do not want customers to have to log in to anything in order to place an order, how can we do 
>this officially? (I realize that the commented-out code did not achieve this, I just did it for 
>aesthetic reasons)

Same as above as far as not using comments. I would have to inspect the way orders are being authenticated but you are probably looking at some moderate PHP work to allow this behavior if it is not included as a standard feature.







###4.)###
>Since Magento interprets / for – 

>how can we keep the naming convention for URLs like 
>`http://www.brick-anew.com/shop/fireplace-glass-doors/page1.html` when putting 
>`/shop/fireplace-glass-doors/page1.html` into the Category URL Key 
>results in `http://magento.brick-anew.com/index.php/shop-fireplace-glass-doors-page1.html`  Won't we 
>lose traffic leading to this link?

I would first investigate why Magento is making this re-write and attempt to ditch it if at all possible. Next I would try escaping my slashes when inputting them using the standard `\` character for escaping:

    \/shop\/fireplace-glass-doors\/page1.html

**Encoding File Paths**

Although escaping in this way may work, it is considered best practice to encode the path to prevent different interpretations of the path between different browsers. 

This can be accomplished via JavaScript's `encodeURI()` function:

    %2Fshop%2Ffireplace-glass-doors%2Fpage1.html

You would then enter that as your Category URL Key and the browser will decode it automatically and treat it as a normal link. 

Online utility I use to encode/decode URL's manually:
   
    http://meyerweb.com/eric/tools/dencoder/




###5.)###
>I've been able to add `<ul>` bullet points to Category pages like 
>`http://magento.brick-anew.com/index.php/fireplace-gas-logs.html` however, I cannot enable them for 
>our home page `http://magento.brick-anew.com/`.
>I tried commenting out the `/* Lists */` and the `ult-callout-list` sections of the `style.css` and 
>`style.uncompressed.css` sheets, neither worked. The `/* Lists */` method showed me the bullet points 
>for all the products and attributes that were being hidden by the CSS (ugly).  So, is there a way to 
>show bullet points on the main page?

**JavaScript/CSS/HTML File Minification**

Let me start by saying that you shouldn't have been editing the `style.uncompressed.css` file because the Magento framework is going to be calling the minified/compressed version at `style.css`. Uncompressed/unminified files are for use in the development environment only, then are minified and deployed live. If your fix HAD been in one of those files, you would have first made the change in `style.uncompressed.css` because it is spaced and formatted to be readable by a human. The formatting, however, isn't necessary for a computer to read it and should be removed before deploying the file live to decrease file size in order to increase site load speed and decrease bandwidth cost. All of these non-essential spaces and comments are known as `syntactic sugar` and must be removed before going live, hence the existence of `style.css` to contain a version that is stripped down to a single huge line of code optimized for a browser's rendering engine but virtually unreadable to a human. 

JS Beautifier is an online utility to format a file. It can unminify a minified JS/CSS/XHTML file but note that you shouldn't ever be moving from minified to unminified but rather the reverse. This tool is best to provide proper indenting, spacing and overall formatting to "beautify" your development version so it is readable and in the proper syntax:

    http://jsbeautifier.org/

One you are ready to minify/compress then online tools exist for this as well:

    http://refresh-sf.com/yui/ 

All development should take place in `style.uncompressed.css`, even though this file will affect nothing on the live site, because this is the only file that is readable by a human eye. Once a change is made the contents of `style.uncompressed.css` should either be run through a build script to minify it automatically or it should be run through any common code minification tool available via text editor plugin or via an online minification tool. Both files will contain exactly the same CSS functionality if done correctly, the only difference being the removal of all unnecessary spaces, comments, etc.

If you want to see the difference in speed that the difference can make you may want to put up an uncompressed file, benchmark site speed, then put up the compressed version and repeat. 

Monitor Site Speed:

    http://www.webpagetest.org/


**Inspector Tools**

Since toying with the CSS didn't help though, I would approach this by using a browser's inspector tool. For example, in later versions of Chrome there is a set of "developer tools" available from the main menu:

    Chrome Main Menu > View > Developer > Developer Tools

The inspection tool is represented by a magnifying glass icon, as is the case in most other browser inspection tools, such as the Firebug plugin for Firefox (link below). Clicking the mag glass icon starts the tool, allowing you to click any page element to see the element's generated html source, which can be notably different from the unrendered html that you would see via a browser's `view source` option. 

Firebug for FireFox:

    http://getfirebug.com/

Also displayed in the inspector tool are all the CSS rules being applied to the element being inspected as well as the location of the CSS file/line applying the rule. Your bullet issue would likely be CSS related as you surmised, but I see bullets currently in Chrome on Mac OS X so I am not sure if you have already fixed this issue. 

**Future Proofing**

On another side note, when I inspected the `<ul>` elements on the home page I am seeing many browser specific prefixes to support CSS definitions that have not been formalized and included in the official releases of CSS standards currently. However, on the elements using prefixes I am not seeing any future proofing in place. "Future proofing" is a way to make sure the code will not break in time with the release of new browsers that may drop support for the prefixes, a likely scenario once the experimental CSS definitions are standardized. Right now on unordered lists on the home page I am seeing properties like `-webkit-margin-end` to provide support for the WebKit rendering engine currently used by Chrome and Safari, as well as `-moz-margin-end` to provide support for Firefox and its Gecko engine. However, I see no subsequent definition for `margin-end` without the prefix. If it can reasonably be assumed that a CSS feature will become standard, a definition such as `margin-end` with no prefix should be specified in addition to the prefixed definitions to future proof the code, so that if prefixes are not supported in future browsers the rule will still be applied if `margin-end` has by that point become standard.







###6.)###
>I've been editing the Ult-Home-Top-Callout Block and I notice that the CSS formatting and "color 
>hover change" features are lost when the Java Script links in each `<td>` are deleted. How can I 
>manipulate these `<td>`s in order to set up my own link paths, while still keeping the Java Script 
>flare and CSS styling? (im sure it'll be in a JS file).  


You will want to again use the inspector tool described above. Inspect the elements with the JS in place and the inspector tool will give you all CSS rules being applied to the element, even if it is being set at runtime with a JS file. Apply the necessary CSS definitions to your new non-JS elements.

Let's say that the inspector tool shows that my elements in question are standard links with a class of `navLink` contained within an HTML5 nav element:


    <nav>    
        <a href="./my/File/Path/Relative/to/Root.html" class="navLink">Link</a>
    </nav>


The links have some CSS style for normal and hovered states, but it is being set via JavaScript on DOM ready:


    $(function(){
    	$('nav .navLink')
    		.css({
    			'background':'#000',
    			'color':'#FFF',
    			'display':'block',
    			'height':'50px',
    			'width':'100px'
			})
    		.hover(function(){
    			$(this)
    				.css({
    					'background':'#FFF',
    					'color':'#000'
    				});
    			});
            })
        ;
    });

If I killed that JS then my example nav links wouldn't show up as styled block elements, instead they would be normal text links. Using the inspector tool on the links with the JS still applying the style will show you all of the properties being applied so you don't have to track down the JS file unnecessarily. Since the inspector reflects what properties are being applied, you can then convert it over to plain CSS declarations for normal and hovered states:


    /* Normal */     
    nav a.navLink {
    	background: #000;
    	color: #FFF;
    	display: block;
    	height: 50px;
    	width: 100px
    }

    /* Hovered */    
    nav a.navLink:hover {
    	background: #FFF;
    	color: #000
    }

**CSS Specificity**

At that point the JS is no longer needed and the affected JS lines should either be removed or, if possible, the entire script call can be removed from the DOM tree to improve browser performance. Note that to maintain best practices in my CSS above I have carried the selectors out to one level of specificity by including `nav` to indicate where target elements are contained, thereby avoiding conflicts with any other instances of a class of `navLink` that may lie outside of the `<nav>` element.At least one level of specificity is considered best practice and I also defined my selector as `a.navLink` rather than `.navLink` to indicate that the properties should only be applied to `<a>` elements of class `navLink` rather than any element with that class.

To understand the hierarchy of CSS selectors and learn more about specificity, Smashing Magazine has a good write up on specificity and selector precedence:

    http://coding.smashingmagazine.com/2007/07/27/css-specificity-things-you-should-know/

Although it may seem cumbersome, these are considered best practices for a huge number of reasons. Most notably, at least one level of specificity helps to avoid naming conflicts that become quite likely with multiple developers and a large code base. By being this specific I'm making sure that I wouldn't be accidentally overwriting any other elements that may have this class but with a different intended set of definitions. Secondly, browser performance is improved through specificity, both in JavaScript and CSS, as it more accurately describes an element's location. Rather than wasting resource overhead by browsing the entire DOM tree for instances of class `navLink`, here the browser's CSS rendering engine is being told it only has to look within the `<nav>` element rather than through the entire document, providing speed benefits. 

**Getting Picky About Speed**

I once worked on a site that averaged 4 million unique visitors a day and with so many visitors we would go to extra lengths to increase speed and reduce database overhead. One method we used included alphabetizing the CSS properties, as in my example CSS above. When a browser renders the document it alphabetizes all CSS property definitions as part of the process and by alphabetizing definitions ahead of time we squeezed out extra performance saving the browser this additional task, just one example that you can get as crazy as you want to if you are trying to optimize your code =)

-Flem





