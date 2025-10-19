---
layout: page
title: All Chapters
permalink: /chapters/
---

## Complete Chapter Listing

This page provides a comprehensive overview of all chapters in Learning React. Each chapter builds upon the previous ones, taking you from basic command line concepts to advanced React development techniques.

{% assign chapters = site.chapters | sort: 'order' %}

{% include chapter-listing.html %}

## Navigation Tips

- **Keyboard users**: Use Tab to navigate between chapter links, Enter to open a chapter
- **Screen reader users**: This is a numbered list with {{ chapters.size }} items
- **All users**: Each chapter includes navigation links to move between chapters sequentially

## Accessibility Features

This chapter listing includes:

- Semantic HTML structure with proper heading hierarchy
- ARIA labels and descriptions for assistive technology
- Keyboard-accessible navigation
- High contrast design meeting WCAG AAA standards
- Responsive design that works at all zoom levels
