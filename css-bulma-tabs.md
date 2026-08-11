---
title: "tabs 实现"
description: "使用bulma"
date: 2022-07-27T00:26:39+08:00
---

# html
Note: The header and body are wrapped with div!
```
<div id="tabs-with-content">
</div>
```

## Header
```html
  <div class="tabs is-centered">
    <ul>
      <li><a>Home</a></li>
      <li><a>Tech Parameters</a></li>
    </ul>
  </div>
```

## Body
```html
  <div class="tabs is-centered">
    <ul>
      <li><a>Home</a></li>
      <li><a>Tech Parameters</a></li>
    </ul>
  </div>
  <div>
    <section class="tab-content">
      <table class="table">
        <tbody>
          <td>
            <%= image_tag "#{@image.name}" %>
          </td>
          <td>
            <%= @product.description %>
          </td>
        </tbody>
      </table>
    </section>
    <section class="tab-content">
      <%= @parameter.ipxx %>
    </section>
  </div>
```

# javascript
```js
let tabsWithContent = (function () {
  let tabs = document.querySelectorAll('.tabs li');
  let tabsContent = document.querySelectorAll('.tab-content');

  let deactvateAllTabs = function () {
    tabs.forEach(function (tab) {
      tab.classList.remove('is-active');
    });
  };

  let hideTabsContent = function () {
    tabsContent.forEach(function (tabContent) {
      tabContent.classList.remove('is-active');
    });
  };

  let activateTabsContent = function (tab) {
    tabsContent[getIndex(tab)].classList.add('is-active');
  };

  let getIndex = function (el) {
    return [...el.parentElement.children].indexOf(el);
  };

  tabs.forEach(function (tab) {
    tab.addEventListener('click', function () {
      deactvateAllTabs();
      hideTabsContent();
      tab.classList.add('is-active');
      activateTabsContent(tab);
    });
  })

  tabs[0].click();
})();
```


# Ref
- [Bulma: Tabs with Content](https://eiji.dev/bulma-tabs-with-content.html)
