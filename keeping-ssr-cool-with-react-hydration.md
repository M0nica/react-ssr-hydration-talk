slide-transition: true
footer: 👩🏾‍💻 www.monica.dev | 🐦 @waterproofheart

![inline](images/cake.png)

# Keeping SSR Cool with React Hydration

## Monica Powell

React Rally 2020

---

# Hi, I'm Monica 👋🏾

I'm a software engineer who enjoys building technology that elevates people whether that's increasing access to e-books, creating tools for Meetup's community organizers or sending curated career opportunities to diverse job-seekers at scale. I'm also passionate about making open-source more accessible and recently became an inaugural GitHub Star 🌟

![right](images/animonica.png)

^ I'm Monica! I'm a software engineer, I am a community organizer and created a Meetup React ladies for women and non-binary react developers. 

---

# Overview

The purpose of this talk is to share some helpful things to keep in mind to render a seamless experience as a Server-Side Rendered (SSR) site transitions from a window-less (server) environment to a browser.

^ Server-side rendering can be powerful but it does require thinking in multiple contexts and I want to share some of the gotchas I've run into while developing Server-Side Rendered websites.




---

# What is Server-Side Rendering (SSR)?

A server generates the initial HTML that loads in a browser. Frameworks like NextJS and GatsbyJS support SSR out-of-the box

^ First what is server-side rendering? [read slide] There are multiple types of Server-Side Rendering for example SSR can be used to render every single page request or only the initial page request. NextJS offers more robust server-side support than Gatsby. In contrast, you may be familiar with Create React App which does NOT come with SSR functionality out of the box.

---

# Why Server-Sider Rendering (SSR)?

SSR apps tend to have faster initial loading times and better SEO than client-side only apps.

---


# What is Client-Side Rendering (CSR)?

![inline](images/enable-javascript.png)

^ With Client-Side Rendering you must have HTML enabled in order to view content on the site. Not this is largely a blank page if a user does not have JavaScript enabled. 

---

# Client-Side Rendered Initial DOM

![inline](images/enable-javascript-code.png)

^ If you look at the DOM in the developer tools of a Create React App (or client-side rendered application) you'll notice very little HTML markup in the DOM. You'll see the root where React is injected, a message saying you need to enable JavaScript to run the app as well as script tags that link to the JavaScript that needs to be loaded to hydrate the page.

---

# Overview of SSR (in static context)

![inline](images/hydration-steps.png)

^ Write site in React ⚛️ -> Gatsby creates a production build of your site using ReactDOMServer, a React server-side API to generate HTML from React. -> Someone visits your website and the HTML generated from the server is displayed on the page -> ReactDOM.hydrate(), hydrates the HTML page that was rendered from the server with JavaScript -> React reconciler APIs take over and the site becomes interactive

---
# Toggling JavaScript

- SSR vs CSR

![left](images/gatsby-toggle-javascript.gif)

![right](images/create-react-app-toggle-javascript.gif)

^ Let's take a look at what happens when JavaScript is enabled or disabled in a SSR or CSR application. I used Gatsby and Create React App as examples

---

![left](images/gatsby-toggle-javascript.gif)

![right](images/create-react-app-toggle-javascript.gif)

^Create-React-App in contrast uses client-side rendering where the browser is responsible for constructing the initial HTML therefore, we just see the bare bones HTML as opposed to a full HTML document with initial content.

---

# Important Note

- User or browser-specific data is not available in the server when static HTML for the site is generated (i.e., window size, authentication status, local storage etc)

- Static-site-generation (SSG) is when all of the pages for a site are generated at build time 

![inline](images/target-nav.gif)

^ In this image you'll see that the store location data, my username and items in shopping cart were not available on initial page load. Patterns like this can be common on server-side rendered applications.

---
# What could go wrong? 😅

 - Layout shifts that only appear in production
 - Errors that only appear at build-time

---

# Debugging SSG Hydration Issues 🐞

![inline](images/what-could-go-wrong.gif)

^ Developing in an SSR context can introduce a new set of issues as you need to consider how the page loads BEFORE JavaScript is available since JavaScript is not required for the page to load. 

---

# Debugging SSG Hydration Issues 🐞

- Disable JavaScript

- View filmstrips to see how the website hydrates

![inline](images/no-javascript.png)

^ You may notice weird changes on initial page load that change too quickly to properly inspect. But there's another way to slow down and really see what is going on. You can disable JavaScript and also use a site like webpage speed test to generate thumbnails that show you exactly how the page is loading step by step.

---

Before: Styled icons with CSS from Font Awesome NPM Package

![inline](images/font-awesome-rendering-issue.png)


^ Here's a waterfall I took of the issue on my site. You can see one of the issues is that the size of the icons changes drastically between 96% and 99% loaded which can be a disjointing experience.

---

![](images/jsstocss.png)

# Solution

- Replicate the final styling with local CSS without relying on FontAwesome's external CSS which required JavaScript to be applied

^ Once I disabled JavaScript, I was able to discover that the ways the icons look before they were fully loaded mirrored the app without JavaScript, I determined font awesome was using its own styling that was coming in through JS. In order to resolve the issue I disabled font awesome's external CSS since it's loaded via JavaScript and replicated the css I wanted locally.

---

After: Styling icons locally and disable Font Awesome's CSS

![inline](images/after-fixing-fontawesome-issue.png)

^ Now you'll notice that the styling of the icons is consistent as the application loads



---

# Conditional Rendering is not your friend


    if (small) {
      return <MobileApp />
    } else {
      return <DesktopApp />
    }
 


---

# Conditional Rendering is not your friend

- There's no way to consistently know the browser size in the server which leads to strange layout shifts when the initial positioning is incorrect. 

- Use CSS media queries directly or a library like arts/fresnel that wraps all `Media` components in css. 
# Error: Document or Window is undefined 

When building a site you might run into the `window is undefined` or `document is undefined` error. This happens when  logic within an app assume the **browser** window is defined in a **server**




---

# Immutable Layout

- Avoid unnecessary layout shifts during page load

- Implement layouts with placeholder/gap for expected client-side content

* Use CSS instead of JS to handle the layout of the page

^Another culprit in the previous example was using JS to position content instead of media queries. CSS loads before JS and is less resource-intensive. This can be a common issue when loading the page as there may still be some data unavailable as the page loads. You ca n try to design around this by leaving room for data to come in, for example in the Target nav there was no shift as the user/store specific data loaded. You should also use CSS instead of JavaScript for styling ;) 

^Learn more at: https://www.speedpatterns.com/patterns/immutable_layout.html

---

# Error: Window is undefined

Accessing browser specific elements in a server context results in errors.

Your first inclination to resolve the undefined Window error might be to write something like:

`typeof window !== undefined ? // render component : // return null`

---

# Error: Window is undefined

However, ReactDOM.hydrate:  
  - expects that the rendered content is identical between the server and the client.
  -  does not guarantee that attribute differences will be patched up in case of mismatches.


---

# Error: Document is undefined

You want to avoid reconciliation error when ReactDOM.hydrates a site from HTML -> React.

It's better to wrap React that can only render when Window or document is defined in useEffect which only fires _after_ the component has mounted. 


---

# Error: Document is undefined

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
}

(example from React docs)



---

> "JavaScript is a powerful language that can do some incredible things, but it’s incredibly easy to jump to using it too early in development, when you could be using HTML and CSS instead...

-- Iain Bean, Your blog doesn’t need a JavaScript framework

---

> ...Consider the rule of least power: Don’t use the more powerful language (JavaScript) until you’ve exhausted the capabilities of less powerful languages (HTML)."

-- Iain Bean, Your blog doesn’t need a JavaScript framework

---


<!-- These slides will be hidden

# Art Direction: Rule of Least Power

![inline](images/art-direction.gif)

^ I want to walk through a time I recently used the rule of least power to make the initial page load faster and reduce relying on JavaScript to dynamically load different images on my site

---

# Art Direction: HTML or Javascript?

- HTML art direction can be used to dynamically load of images based on screen size using srcset attributes with `<img>` and `<source>` instead of JavaScript.

---

# Art Direction: Why HTML?

- Swapping images at different screen sizes can be done with JavaScript or CSS instead of native HTML attributes however using HTML can improve page loading performance as it prevents unnecessarily preloading two images


---


# Art Direction in HTML

![inline](images/animonica-art-direction.png)

^In order to set up this functionality in HTML you can use the picture attribute and set media queries on each source image. It will return the first condition the is true and as a fall back it'll return the image from the img tag.


---


![inline](images/art-direction-gatsby.png)


---

-->
# Summary

- In a Server-Side Rendered context it's important to consider _how_ the page loads and what data is or is not available on initial page load.
- CSS is a better tool for managing responsiveness
- Reference browser specific elements like document or window directly within `useEffect()` to prevent reconciliation error as the page hydrates


---

# Related articles:

https://www.aboutmonica.com/blog/less-javascript-is-more 

https://www.aboutmonica.com/blog/2020-06-24-exploring-art-direction-in-gatsby



---
# Resources
- https://www.gatsbyjs.org/docs/react-hydration/
- https://joshwcomeau.com/react/the-perils-of-rehydration/
- https://reactjs.org/docs/reconciliation.html
- https://www.webpagetest.org/
- https://github.com/artsy/fresnel




---
# Thank You!

## See you in CYBERSPACE 👋🏾

@waterproofheart

👩🏾‍💻 www.monica.dev
