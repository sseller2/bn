>1) is there a way to simply link a cms page to the Exploded Menu? All the "hacks" I've found in the 
>forum do not work.  We need to do this to respect the URL conventions we've already established. For 
>instance (http://www.brick-anew.com/brick-anew-policies.html) on our x-cart site would be 
>(http://magento.brick-anew.com/index.php/articles-home-improvement/brick-anew-policies.html) 
>if added to the exploded menu on the new site, since the "articles-home-improvement" portion 
>would be inherited from it's parent category "Resources" in the nav. Thus, we would lose 
>traffic to the original link. 

There are multiple ways to do this. To accomplish what you are asking you need a `301 redirect` that would go within the `.htaccess` config file in the root directory of the new Magento site (assuming the current server is run off Apache). About 80% of servers are Apache but note that this solution is different if the server is a Microsoft IIS based system, CentOS based, etc.

Google's Answer:
    http://support.google.com/webmasters/bin/answer.py?hl=en&answer=93633

You would redirect your old URL to your new one to prevent search engine links becoming broken and thus avoid all pages losing their SEO reputation.

An example redirect:
    RewriteEngine on
    RewriteCond %{HTTP_HOST} ^www\.brick-anew\.com$
    RewriteRule ^(.*)$ http://www.magento.brick-anew.com [R=301,L]

That example would redirect all `www.brick-anew.com` requests to `www.magento.brick-anew.com`. In this way your link in the exploded menu could be to the new URL or to the old URL since one is being forwarded to the other. Be sure to use relative rather than absolute paths for all links, image sources and external code sources, like JavaScript files.

Finally, be sure to hide `index.php` in the url using these redirects. The `index.php` portion of the URL is negative because it serves no purpose in describing the link, making it unnecessarily complicated and more likely to be truncated in search results. 

Showing any `.php` filenames in this way allows an attacker to more readily profile a target for vulnerabilities and ways to break past site security. Because I know just from the URL that the site uses PHP, I automatically know that it may be vulnerable to SQL injection or XSS scripting attacks. A file extension of `.php` is also debated in SEO circles because it is an immediate red flag that a site is being served from a database, thus more likely to be spam.








>2) If not, can we get rid of the blue attribute column that generates on every "Category/ CMS Block" 
>page? The column that helps refine search based on attributes (e.g., "Compare Products")
>
>Second topic: I commented out the "ultcustomernav.phtml" page in order to get rid of the "My Cart and 
>Account" section from the Nav Menu visually. 

I'm not yet familiar with the layout but what you are describing sounds like a poor solution to achieve what you are trying to accomplish. If you comment code out but leave the associated containing element, such as a surrounding div, then that code still has to be parsed and acted upon by the browser. 

Although nothing comes through on the front end, browser overhead is being created to read through comments, which in turn slows the site speed. No comments should ever be displayed on a live production site, all should be stripped via a build file to ensure any code commenting is restricted to your development environment.

Instead of commenting code out and if it is impossible to have the items removed before deployment, the containing element should be removed from the DOM completely to avoid the browser being forced to act, as described above.

Remove div w/ id "myDiv" using jQuery, after DOM ready:
    $(function(){
    	$('#myDiv').remove();
    });
    






>3) We do not want customers to have to log in to anything in order to place an order, how can we do 
>this officially? (I realize that the commented-out code did not achieve this, I just did it for 
>aesthetic reasons)

Same as above as far as not using comments. I would have to inspect the way orders are being authenticated but you are probably looking at some moderate PHP work to allow this behavior if it is not included as a standard feature.








>4) Since Magento interprets / for – 

>how can we keep the naming convention for URLs like (http://www.brick-anew.com/shop/fireplace-glass-
>doors/page1.html) when putting (/shop/fireplace-glass-doors/page1.html) into the Category URL Key 
>results in (http://magento.brick-anew.com/index.php/shop-fireplace-glass-doors-page1.html)  Won't we 
>lose traffic leading to this link?

I would first investigate why Magento is making this re-write and attempt to ditch it if at all possible. Next I would try escaping my slashes when inputting them using the standard `\` character for escaping:

    \/shop\/fireplace-glass-doors\/page1.html

Although escaping in this way may work, it is considered best practice to encode the path to prevent different interpretations of the path between different browsers. 

This can be accomplished via JavaScript's `encodeURI()` function:

    %2Fshop%2Ffireplace-glass-doors%2Fpage1.html

You would then enter that as your Category URL Key and the browser will decode it automatically and treat it as a normal link. 






>5) I've been able to add <ul> bullet points to Category pages like (http://magento.brick-anew.com/index
>.php/fireplace-gas-logs.html) however, I cannot enable them for our home page (http://magento.brick-
>anew.com/).
>I tried commenting out the "/* Lists */" and the "ult-callout-list" sections of the style.css and style
>.uncompressed.css sheets, neither worked. The "/* Lists */" method showed me the bullet points for all 
>the products and attributes that were being hidden by the CSS (ugly).  So, is there a way to show 
>bullet points on the main page?

I would approach this by using an inspector tool. For example, in later versions of Chrome there is a set of "developer tools" available from the main menu:

    Chrome Main Menu > View > Developer > Developer Tools

The inspection tool is represented by a magnifying glass icon, as is the case in most other browser inspection tools as well. Clicking the icon starts the tool, allowing a developer to click any page elements to see the element's generated/rendered html source, which can be notably different from the unrendered html that you would see via a browser's `view source` option. 

Also displayed in the inspector tool is all the CSS rules being applied to the element being inspected, as well as the location of the CSS applying the rule. Your bullet issue would likely be CSS related as you spotted, and I see bullets currently in Chrome on Mac OS X so I am not sure if you have already fixed this issue. 

On a side note, when I inspected the `<ul>` elements on the home page I am seeing many browser specific prefixes to support CSS definitions that have not been formalized and included in the official releases of CSS standards currently. However, on the elements using prefixes I am not seeing any future proofing in place. "Future proofing" is a way to make sure the code will not break in time with the release of new browsers that may drop support for the prefixes, a likely scenario once the experimental CSS definitions are standardized. Right now on unordered lists on the home page I am seeing properties like `-webkit-margin-end` to provide support for the WebKit rendering engine currently used by Chrome and Safari, as well as `-moz-margin-end` to provide support for Firefox and its Gecko engine. However, I see no subsequent definition for `margin-end` without the prefix. If it can reasonably be assumed that a CSS feature will become standard, a definition such as `margin-end` with no prefix should be specified in addition to the prefixed definitions to future proof the code, so that if prefixes are not supported in future browsers the rule will still be applied if `margin-end` has by that point become standard.








>6) I've been editing the Ult-Home-Top-Callout Block and I notice that the CSS formatting and "color 
>hover change" features are lost when the Java Script links in each <td> are deleted. How can I 
>manipulate these <td>s in order to set up my own link paths, while still keeping the Java Script 
>flare and CSS styling? (im sure it'll be in a JS file).  

You will want to use the inspector tool described above. Inspect the elements with the JS in place and the inspector tool will give you all CSS rules being applied to the element, even if it is being set at runtime with a JS file. Apply the necessary CSS definitions to your new non-JS elements.

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

If I killed that JS then my example nav links wouldn't show up as styled block elements, instead they would be normal text links. Using the inspector tool on the links with the JS still applying the style will show you all of the properties being applied so you don't have to track down the file unnecessarily. Since I see what properties are being applied, I can then convert it over to plain CSS declarations for normal and hovered states:

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

At that point the JS is no longer needed and the affected JS lines should either be removed or, if possible, the entire script call can be removed from the DOM tree to improve browser performance. Note that to maintain best practices in my CSS I have carried the definition out to one level of specificity by including `nav` as the element where the links are contained, thereby avoiding conflicts with any possible instances of a class of `navLink` that may lie outside of the `<nav>` element. At least one level of specificity is considered best practice. Furthermore I defined my selector as `a.navLink` rather than `.navLink` to indicate that the properties should only be applied to `<a>` elements of class `navLink` rather than any element with that class.

Although it may seem cumbersome, these are considered best practices for a huge number of reasons. Most notably, at least one level of specificity helps to avoid naming conflicts that become quite likely with multiple developers and a large code base. By being this specific I'm making sure that I wouldn't be accidentally overwriting any other elements that may have this class but with a different set of style definitions. Secondly, browser performance is improved through specificity both in JavaScript and CSS because it is a more accurate description of where the element is located. Rather than wasting resource overhead by browsing the entire DOM tree for instances of `navLink`, now the browser's CSS rendering engine is being told it only has to look within the `<nav>` element rather than through the entire document. The process for the engine to search and apply the style properties then becomes much faster and further helps site speed.




