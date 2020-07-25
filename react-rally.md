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

---

# Overview

The purpose of this talk is to share some helpful things to keep in mind to render a seamless experience as a Server-Side Rendered (SSR) site transitions from a window-less (server) environment to a browser.

---

# What is Server-Side Rendering (SSR)?

A server generates the initial HTML that loads in a browser from JavaScript. Frameworks like NextJS and GatsbyJS support SSR out-of-the box

Create-React-App in contrast uses client-side rendering where the browser is responsible for constructing the initial HTML.

---

# Toggling JavaScript

- GatsbyJS vs. Create React App

![left](images/gatsby-toggle-javascript.gif)

![right](images/create-react-app-toggle-javascript.gif)

---

![left](images/gatsby-toggle-javascript.gif)

![right](images/create-react-app-toggle-javascript.gif)

---

# Client-side rendering

![inline](images/enable-javascript.png)

---

# Client-side rendering

![inline](images/enable-javascript-code.png)

---

# Overview of SSR in Gatsby

    1. Write site in React ⚛️

    2. Gatsby creates a production build of your site using ReactDOMServer, a React server-side API to generate HTML from React.

    3. Someone visits your website and the HTML generated from the server is displayed on the page.

    4. ReactDOM.hydrate(), hydrates the HTML page that was rendered from the server with JavaScript

    5. After ReactDOM hydrates the page with JavaScript, the React reconciler takes over to efficiently handle component updates

---

# Important Notes

- User or browser-specific data is not available in the server when HTML for the site is generated (i.e., window size, authentication status, local storage etc)

![inline](images/target-nav.gif)

---

# What could go wrong? 😅

![inline](images/what-could-go-wrong.gif)

---

# Reproduce Initial State

- Disable JavaScript

- View filmstrips to see how the website hydrates

![inline](images/no-javascript.png)

---

Before: Styled icons with CSS from Font Awesome NPM Package

![inline](images/font-awesome-rendering-issue.png)

---

![](images/jsstocss.png)

# Solution

- Replicate the final styling with local CSS without relying on FontAwesome's external CSS which required JavaScript to be applied

---

After: Styling icons locally and disable Font Awesome's CSS

![inline](images/after-fixing-fontawesome-issue.png)

---

# Immutable Layout

- Avoid unecessary layout shifts during page load

- Implement layouts with placeholder/gap for expected client-side content

* Use CSS instead of JS to style the page

^Another culprit in the previous example was using JS to position content instead of media queries. CSS loads before JS and is less resource-intensive

^Learn more at: https://www.speedpatterns.com/patterns/immutable_layout.html

---

> "JavaScript is a powerful language that can do some incredible things, but it’s incredibly easy to jump to using it too early in development, when you could be using HTML and CSS instead...

-- Iain Bean, Your blog doesn’t need a JavaScript framework

---

> ...Consider the rule of least power: Don’t use the more powerful language (JavaScript) until you’ve exhausted the capabilities of less powerful languages (HTML)."

-- Iain Bean, Your blog doesn’t need a JavaScript framework

---

# Using rule of least power to dynamically load images

![inline](images/art-direction.gif)

---

# Using rule of least power to dynamically load images

- HTML art direction can be used to dynamically load of images based on screen size using srcset attributes with `<img>` and `<source>` instead of JavaScript.

---

# Using rule of least power to dynamically load images

- Swapping images at different screen sizes can be done with JavaScript or CSS instead of native HTML attributes however using HTML can improve page loading performance as it prevents unnecessarily preloading two images

---

# Art Direction in HTML

![inline](images/animonica-art-direction.png)

---

# Art Direction in Gatsby

![inline](images/art-direction-gatsby.png)

---

![inline](images/art-direction-gatsby.png)

---

# Output of Gatsby Image Art Direction Resembles

![inline](images/animonica-art-direction.png)

---

# Thank You!

## See you in CYBERSPACE 👋🏾

@waterproofheart

👩🏾‍💻 www.monica.dev
