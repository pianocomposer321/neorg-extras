# Page View

![Neorg Page View](https://i.imgur.com/DdVAiQX.png)

This will show all tasks filtered by the provided task states and turn it into
a paginated view.

`Neorg agenda page <task-states>`

You can list out all possible Neorg task states:

- `ambiguous`
- `cancelled`
- `done`
- `hold`
- `pending`
- `important`
- `recurring`
- `undone`

**Examples**

`Neorg agenda page undone pending` <- Will open agenda view with all pending
and undone tasks in current workspace.

Tasks are currently segregated by the nodes they are found in. You can hit
`<CR>` over the node names to navigate to those nodes as they are hyperlinks.

The agenda view can be closed with `q`.

# Day View (TM)

![Neorg Day View](https://i.imgur.com/Qaptzbq.png)

This will sort tasks according to a day view. Something similar to
`org-agenda` but with my own flavour.

Note that the method I am using to assign "metadata" to tasks is not the
official way to do things. That is still awaiting an update of the tree-sitter
parser. I don't know how long that will take. And as someone who wants to
organise their life yesterday, I can't afford to wait that long. Hence:

`Neorg agenda day`

You will notice that all of your tasks in the workspace are uncategorised. This
is because we haven't added the property `ranged_verbatim_tag` that is used
in this plugin-plugin (plugin^2?) to define the sorting categories for the
agenda. You can populate the property tag by checking out [this](#the-property-metadata).

# Tag View

![Neorg Tag View](https://i.imgur.com/GEspcD3.png)

You can also sort tasks by tags. This is useful if you want to sort them
according to projects or pertinent categories. Make sure to include tags in the
property metadata under your tasks if you want these to be sorted.

`Neorg agenda tag`

# The Property Metadata

```norg
@data property
started: YYYY-MM-DD|HH:MM
completed: YYYY-MM-DD|HH:MM
deadline: YYYY-MM-DD|HH:MM
tag: tag1, tag2, ...
priority: A/B/C ...
@end
```

There is tree-sitter folding for the `ranged_verbatim_tag` labeled `data`.
So it no longer will take up your visual space.

Of course, this plug(plugin)in(?) makes your life a lot easier by exposing the
following function:

`Neorg update_property_metadata`

This function doesn't discriminate between task headings or regular headings
(at the moment... I will fix that later).

This function is multi-purpose:

1. It can add the property tag to the current heading if it doesn't have one
already.
2. It will update the property tag under the current heading if it has one
already

# Sorting-out my Life

I would suggest that you open your Day View (TM) and start going through your
uncategorised tasks and start assigning them a started and deadline property.
Maybe add a tag? You could also add a property (but I haven't implemented
support for finer sorting yet).

# Interactive Task Cycling

Manually changing task states and updating metadata to reflect that can grow
tedious after a point. Its basically doing the same thing twice. And who wants
to waste time looking at a clock and writing down the time to log their tasks?
Vim is (after all) all about reducing redundancy and wasted time, and so the
command:

`Neorg cycle_task`

Compresses task state update and property metadata update into one. Calling
this function on a heading that is or isn't a task will initiate the
interactive task cycling process. The general sequencing of task states are as
follows:

```txt
# Task Transitions
undone -> cancelled
undone -> pending -> done
undone -> pending -> cancelled
undone -> pending -> hold -> pending -> done
undone -> pending -> hold -> pending -> cancelled

undone -> ambiguous -> cancelled
undone -> ambiguous -> pending -> done
undone -> ambiguous -> pending -> cancelled
undone -> ambiguous -> pending -> hold -> pending -> done
undone -> ambiguous -> pending -> hold -> pending -> cancelled
undone -> important -> cancelled
undone -> important -> pending -> done
undone -> important -> pending -> cancelled
undone -> important -> pending -> hold -> pending -> done
undone -> important -> pending -> hold -> pending -> cancelled

undone -> recurring -> done
undone -> recurring -> cancelled
```

Furthermore, the time properties are populated automatically given the
following state changes:
- When creating task -> populate deadline
- When leaving undone -> populate started
- When entering cancelled or done -> populate completed

With this command, I hope that users will waste less time manually messing
around with the property metadata, and focus more on getting things done.

