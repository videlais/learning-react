---
layout: page
title: All Chapters
permalink: /chapters/
---

## Complete Chapter Listing

This page provides a comprehensive overview of all chapters in Learning React. Each chapter builds upon the previous ones, taking you from basic command line concepts to advanced React development techniques.

{% assign chapters = site.chapters | sort: 'order' %}

<nav aria-label="Chapter listing" role="navigation">
  <ol class="chapters-list" aria-describedby="chapters-description">
    {% for chapter in chapters %}
    <li class="chapter-item">
      <h3>
        <a href="{{ chapter.url | relative_url }}" aria-describedby="chapter-{{ chapter.order }}-desc">
          {% if chapter.chapter_number and chapter.chapter_number > 0 %}
            Chapter {{ chapter.chapter_number }}: 
          {% endif %}
          {{ chapter.title }}
        </a>
      </h3>
      {% if chapter.excerpt %}
        <p id="chapter-{{ chapter.order }}-desc" class="chapter-description">
          {{ chapter.excerpt | strip_html | truncatewords: 30 }}
        </p>
      {% endif %}
    </li>
    {% endfor %}
  </ol>
</nav>

<p id="chapters-description" class="screen-reader-text">
  A numbered list of all chapters in Learning React, with links to each chapter and brief descriptions.
</p>

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