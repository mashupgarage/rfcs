---
submitted_by: Rico Sta. Cruz (@rstacruz)
submitted_on: 2019-02-28
added_on: 2019-03-13
---

## Summary

**Let's have a standard way of proposing things within MG.** When someone has an idea for something that can be implemented team-wide or organization-wide, let's do so in an _RFC Document_.

This will give everyone a voice on changes they want to see happen in MG, tech-related or otherwise. This will also give us a forum to discuss proposals from the entire organization in one place.

## What is an RFC?

An RFC is a proposal that can be written by anyone. Everyone is also invited to share their comments on submitted RFC's.

RFC stands for "Request for Comments." It's a short document detailing your proposal, and an invitation for your colleagues to leave their comments on it.

In our context, an RFC is an **Issue** posted in the [mashupgarage/wiki](https://github.com/mashupgarage/wiki) repository.

## What can be RFC's?

Almost anything! In particular, tech changes are great as RFC's. Some examples include:

- **RFC: Use contexts in Rails** - Let's implement Phoenix-style contexts in Rails projects.

- **RFC: Use TypeScript** - Let's use TypeScript for type annotations in projects to decrease our bugs.

- **RFC: Deprecate jQuery** &mdash; Let's avoid using jQuery in our projects (with details on how to accomplish this as part of the RFC).

Other non-tech matters can be written as RFC's, too.

## How do I write an RFC?

[Open an issue in mashupgarage/wiki](https://github.com/mashupgarage/wiki/issues). Put details of your proposal.

## RFC Lifecycle

### Stage 0: Draft

Anyone is free to write an RFC. Simply create an issue in the mashupgarage/wiki outlining the details of your proposal.

It will be posted automatically in the `#rfcs` channel of MG's Slack. From here, anyone can leave comments on how it can be made better.

### Stage 1: In consideration

An RFC will be promoted to _In consideration_ by the management team if it's seen as worthy of discussion.

An RFC is likely to change at this point. The team is likely to be engaged in discussing details and alternate solutions, which should all be considered before accepting the RFC proposal.

A tag `S1 (Under consideration)` will be applied to the issue.

Before a proposal moves to the next phase, there should be a plan in place to implement it. This plan could already be partially-done (eg, an RFC for TypeScript may already have TypeScript implemented for a small project).

### Stage 2: Accepted

The management team can promote an RFC to _Accepted_ once the plan is good to go.

(TODO: should we add them to the repo as `.md` files at this point?)

### Rejected

An RFC can be rejected by management. A rejection usually comes with a clear explanation of the reason for doing so.

## How does an RFC look like?

You are looking at one right now. RFC's would typically have these sections:

1. **Summary** &mdash; A few paragraphs detailing what you want to happen. Start this off with a sentence like "Let's _(do this thing)_ to _(satisfy this objective)_".

2. **Details** &mdash; Outline some details on how you would propose for this to happen.

3. **Prior art** &mdash; Links to any projects or organizations who may be implementing similar things to your proposal.

4. **References** &mdash; Links to related things.

## Implementation plan

- Create a new repo, <https://github.com/mashupgarage/rfcs>, to host RFC's.
- Post new RFC's as issues.
- Discuss RFC's in our `#rfcs` Slack channel.

## Prior art

- The React team keeps a GitHub repository, [reactjs/rfcs](https://github.com/reactjs/rfcs), to invite the public to write their proposals on improvements for React. Hooks, a highly-anticipated feature of React, started as a humble RFC: https://github.com/reactjs/rfcs/pull/68

- Rust (programming language) keeps a similar RFC repo: [rust-lang/rfcs](https://github.com/rust-lang/rfcs).

## References

- [Wikipedia page on RFC's](https://en.wikipedia.org/wiki/Request_for_Comments) _(en.wikipedia.org)_
- [React RFC's repository](https://github.com/reactjs/rfcs) _(github.com)_
