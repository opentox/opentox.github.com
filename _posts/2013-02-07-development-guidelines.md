---
layout: post
title: "Development guidelines"
description: ""
category: Development
tags: []
---
{% include JB/setup %}

- Read and understand the [Architecture overview](/Development/2013/02/07/architecture-overview)
- Follow the guidelines for [Error handling](/Development/2012/07/20/error-handling)
- Add tests to [opentox-test](http://github.com/opentox/opentox-test) for your new features
- Make sure that *all* tests in [opentox-test](http://github.com/opentox/opentox-test) pass before committing to an official branch (development, master)

Try to reduce complexity in order to keep the system maintainable and stable:
- Follow the [DRY](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle
- Avoid dependencies (e.g. gems, backends), but don\'t reinvent the wheel
- Implement only required/requested features, avoid features that "might be needed someday"
- Avoid workarounds imposed/requested by other webservice developers. Get in contact with them and convince them to adhere to agreed API standards. Make recommendations to improve APIs if they lead to overly complicated implementations.
- Revise design decisions and refactor if you observe that code is accumulating due to former decisions
- Write clear, self documenting code
