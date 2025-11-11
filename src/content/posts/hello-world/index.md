---
title: 'Hello World'
published: 2025-11-11
draft: false
tags: ['astro', 'blog', 'markdown']
toc: true
---

Hello, my new blog!

This blog is created using [Astro](https://astro.build) with the [MultiTerm](https://astro.build/themes/details/multiterm/) theme on November 11, 2025, and deployed on CloudFlare Pages.

## Installation

Install the required packages:

You can also refer to the README.md in the theme's GitHub [repository](https://github.com/stelcodes/multiterm-astro).

```bash
pacman -S nodejs npm git
```

Then clone the repository:

```bash
git clone --depth 1 https://github.com/stelcodes/multiterm-astro my-new-blog && cd my-new-blog
```

Install dependencies and go:

```bash
npm install
npm run dev
npm run build && npm run preview
```

## More

To learn about some in-depth configuration and how to post, please refer to this [post](./showing-off-blog-features) and the offical docs in the GitHub [repository](https://github.com/stelcodes/multiterm-astro).

## Deploy on CloudFlare Pages

Create a new GitHub repository and commit the full `my-new-blog` dir. Then login CloudFlare Dashboard.

Create a Pages app by importing an existing Git repository, and then select the Astro framework present. Save and depoly and then you will see your site.
