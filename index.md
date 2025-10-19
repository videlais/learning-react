---
layout: page
title: Learning React
---

## Welcome to Learning React

Learning React is an open source textbook designed to be remixed, reused, or improved through submitting issues and pull requests on GitHub!

This comprehensive guide can be accessed in multiple ways:

- **On this website**: Optimized for screen readers and accessible browsing
- **GitHub repository**: [View as processed markdown on GitHub](https://github.com/videlais/learning-react)
- **GitHub Pages**: [Browse the HTML version](https://videlais.github.io/learning-react/)

## What This Book Covers

The word "Learning" in the title *Learning React* is purposeful. This is not an exhaustive reference of everything in React and how to use it.

This book outlines and reviews major React functionality. Its purpose is to help someone *learning* React move into more advanced functionality at their own pace. Think of this as a *crash course* in React functionality and concepts.

## Complete Chapter Listing

{% assign chapters = site.chapters | sort: 'order' %}

{% include chapter-listing.html %}

## Getting Started

**New to React?** Start with [Chapter 1: Introduction to the Command Line]({{ site.chapters | where: "order", 1 | first | map: "url" | first | relative_url }}) and work through each chapter sequentially.

**Looking for something specific?** Use the [complete chapter listing]({{ "/chapters/" | relative_url }}) to jump to any topic.

## Accessibility

This website is designed to meet WCAG 2.1 AAA accessibility standards. Features include:

- Full keyboard navigation support
- Screen reader optimization
- High contrast design
- Responsive layout that works at all zoom levels
- [Complete accessibility statement]({{ "/accessibility/" | relative_url }})

## License Information

### Text and Images

The text and images of this book are licensed under the [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).

**For academic use**, please cite as:

> Cox, D. (2020). Learning React. GitHub repository. <https://github.com/videlais/learning-react>

### Code Examples

All code examples are released under a [Public Domain (CC0 1.0 Universal) license](https://creativecommons.org/publicdomain/zero/1.0/). You are free to use them for any commercial, hobby, or educational purpose without citation, attribution, or acknowledgement.

### Trademarks

React, Facebook, WebPack, Babel, and other tools mentioned are trademarked to their respective organizations or companies.
