---
title: Kubernetes
nav_order: 1
has_children: true
permalink: /k8s/
---

# Kubernetes

K8s에 대한 설명입니다.

<div style="display: grid; gap: 1rem; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));">

{% assign children = site.pages | where: "parent", "Kubernetes" | sort: "nav_order" %}
{% for child in children %}

  <div style="border: 1px solid #e0e0e0; border-radius: 8px; padding: 1rem; background-color: #f9f9f9; box-shadow: 0 1px 3px rgba(0,0,0,0.05);">
    <h3 style="margin-top: 0;"><a href="{{ child.url }}" style="text-decoration: none; color: #2c3e50;">{{ child.title }}</a></h3>
    <p style="margin: 0 0 1rem;">{{ child.excerpt | markdownify }}</p>
    <a href="{{ child.url }}" style="font-weight: bold; color: #3498db;">Read more →</a>
  </div>
{% endfor %}

</div>
