---
layout: article
title: Thoughts on Node.js's event emitter
categories: coolaj86 cs
updated_at: 2010-09-03
---

In general I like the way that Node.js's event emmitter works.
In particular, I like that the convention in node is to pass `err` as the first argument to any futurific function.
However, I don't like that it uses strings.
The problem with things is that they aren't self-documenting.
You can't `JSON.stringify(Object.keys(emitter))` and discover which events it emits.
That's the one thing I would change.
