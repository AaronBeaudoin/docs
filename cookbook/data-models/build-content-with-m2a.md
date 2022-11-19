---
title: Build Content With M2A
description: This recipe demonstrates how to use an M2A relationship to build content dynamically.
tags: []
skill_level:
directus_version: 9.18.1
author_override:
author: Eron Powell
---

# {{$frontmatter.title}}

> {{$frontmatter.description}}

:::tip Author: {{$frontmatter.author}}

**Skill Level:** {{$frontmatter.skill_level}}\
**Directus Version:** {{$frontmatter.directus_version}}\
**Tags:** {{$frontmatter.tags.join(", ")}}

:::

## Explanation

Directus provides a special m2a Interface which makes it quick and easy to build data and content. This Interface is
handy whenever you have a category of content made from varying types of sub-content. To demonstrate this, let's imagine
a personal portfolio site with three pages composed of the following content:

- A `Resume` page composed of:
  - `text` noting your education, work experience, and skills.
  - `user` details, such as your email, phone number, social media links, etc.
- A `Portfolio` page composed of:
  - `text` describing work projects.
  - `images` to demonstrate work projects.
  - `video` to demonstrate work projects.
- An `About` page composed of:
  - `text` to talk about your life, hobbies, or career goals.
  - `images` to show off your life and highlight your personality.
  - `video` so people can see you speak and get an idea of your personal presence.
  - `user` details such as your headshot (linked from `images`) or location.

All three pages are unique in structure, yet composed of the same basic types of content: `text`, `images`, `video`, and
`directus_user` details. For any data or content meet those same general conditions, an M2A content builder may be a
suitable tool.

## The Recipe

:::tip Requirements

You'll need a basic understanding of [data models](/configuration/data-model.md) and
[permissions](/configuration/users-roles-permissions.md) in Directus.

:::

<video autoplay playsinline muted loop controls>
	<source src="" type="video/mp4" />
</video>

### Create the Initial Collections

1. [Create collections](/configuration/data-model/collections.md#create-a-collection) and
   [add fields](/configuration/data-model/fields.md#create-a-field-advanced) as follows:

```
text
- id
- text_content (a standard input field with a Markdown Interface)
```

```
videos
- id
- video_url (a standard input field)
```

:::tip

In Directus, your `user` and `images` collections already exist. They are the built-in
[system collections](/configuration/data-model/collections.html#system-collections): `directus_files` and
`directus_users`. If you'd like to add geo-coordinates or social media links to `directus_users`, add fields as desired.

:::

### Configure an M2A

Now we need to create a `pages` collection, which will contain our M2A relationship.

2. Create the `pages` collection.

```
pages
- id
- title (a standard STRING input field, to store the page title)
- page_content (an M2A with a content builder Interface)
```

3. [Configure the field](/configuration/data-model/fields.md#configure-a-field) `pages.page_content` so its related to:
   - `text`
   - `videos`
   - `directus_users`
   - `directus_files`

At this point, our basic data model configurations are done.

4. [Configure permissions](/configuration/users-roles-permissions/permissions.md#configure-permissions) for each data
   model as desired. You'll likely want to give Public read permissions to the collections we made and configure custom
   access permissions for the `directus_users` and `directus_files`.

### Build and Display Content

5. [Create items](/app/content/items.md#create-an-item) for your `Resume`, `Portfolio`, and `About` pages.

## Final Tips

Next, you'll want to access these with the API. If you try to use `/items/pages` then `page_content` returns an array of
IDs. Instead, you'll want to add a [field parameter](/reference/query.html#many-to-any-union-types) to get nested
relational data.

There are a few important things to keep in mind when using an m2a to build content.

### Is it necessary?

The use-case in our recipe is for a three-page portfolio site, which is quite simple overall.

There's a fair amount of complexity involved with an M2A in the data model, but also in the frontend, as you'll have to
build frontend logic that iterates through each page item, identifies its constituent content types, and injects each
one into an appropriate component or template. In the case of a three-page portfolio blog, that frontend development may
be more complexity than you're bargaining for. You could instead opt for a
[Singleton](/getting-started/glossary.html#singleton) collection and then create three separate _(but less complex)_
pages.

Alternatively, if you plan to have more and more unique pages composed of the same content-types, using an m2a with more
complex frontend logic may end up being more efficient than countless Singletons.

### Is M2A only for content?

Although the Directus [m2a Interface]() was designed with content building in mind, the underlying data model is not
limited to any specific data types. You could use it for whatever you wanted, from _inventory to IoT networks and
beyond!_