# Introduction to the platform

The stack of Fliplet is mostly based on **Javascript** — even server-side — therefore being familiar with it will be a strong requirement in building components and themes on our platform.

We also support some preprocessors like [SASS](http://sass-lang.com/) and templating engines like [Handlebars](http://handlebarsjs.com/). You're not required to use them, but they can boost your development quite a lot when building complex components.

---

## What can you build

### 1. App components

App components will enable your apps to run Javascript on all screens of a Fliplet App. CSS files and HTML/Handlebars templates will also be available on all screens.

Typical usage:
- Analytics
- Global settings for apps

### 2. Page components

Page components are similar to app components, but they also display output since they can be dropped onto a page.
A page component can be dropped more than once on a page, and can be displayed inline or as a block element.

Typical usage:
- Buttons
- Charts
- Video players
- Lists

### 3. Themes

As the name suggests, themes enables you to customise any visual aspect of your apps. A Fliplet App can only be assigned to one theme.

A theme consists in CSS (or SCSS) and Javascript files.

---

## Development tools

Our primary development tool is called [Fliplet CLI](https://github.com/Fliplet/fliplet-cli) (Command Line Interface), it's written in [Node.js](https://nodejs.org) and is available through [Npm](https://www.npmjs.com/package/fliplet-cli).

This means in order to start the development you will need to install [Node.js](https://nodejs.org) on your machine.

The **Fliplet CLI** will enable you to develop and test components and themes on your machine, without having to rely on our APIs or even be connected to the internet while coding.

Check the [Quickstart](Quickstart.md) section for more details about getting started with the development.

<a href="Quickstart.html" class="btn">Go to quickstart</a>