# Notes from https://google.github.io/eng-practices/ on code changes and review.

## A note

Google refer to "changelist" which is the terminology used by some version
control systems. A changelist is a set of changes to source that represents
some logical modification to it, i.e. very much like a pull request.

## Introduction

Code review is a process where someone other than the author(s) of a piece of
code examines the code.

### What do code reviewers look for?

Should look at:

* **Design**: Is the code well-designed and appropriate for your system?
* **Functionality**: Does the code behave as the author likely intended?
  Is the way the code behaves good for its users?
* **Complexity**: Could the code be made simpler? Would another developer be
  able to easily understand and use this code when they come across it in the
  future?
* **Tests**: Does the code have correct and well-designed automated tests?
* **Naming**: Did the developer choose clear names for variables, classes,
  methods, etc.?
* **Comments**: Are the comments clear and useful?
* **Style**: Does the code follow style guides?
* **Documentation**: Did the developer also update relevant documentation?

### Picking best reviewers

Pick the best reviewers you can who can respond to a review in a reasonable
time.

Best reviewer is the person who can give you the most thorough and correct
review for the code you are wriing. This could be different people to review
different parts of it. If you find an ideal reviewer, but they are not
available, you should also make sure they are at least notified of the changes.

### In-person reviews

Pair-programming code with someone qualified to do a good review on it could be
considered reviewed.

Can do in-person reviews where the reviewer asks questions and the developer
only speaks when spoken to.

## For reviewers

### The Standard of Code Review

Code review should aim to improve code health over time. But there are
trade-offs.

Developers must be able to get improvements into codebase, but this must be
balanced by reviewers trying to maintain the overall health of the codebase.

Key principle:

**Reviewers should favour approving a changelist once it is in a state where it
improves the health of the system being worked on, even if the changelist is not
perfect.** 

That said, a reviewer might deny approval if a changelist adds a feature that
the reviewer does not want to add to the system.

No such thing as perfect code; only better code. Reviewers shouldn't require
that the author polishes every tiny piece of a changelist. The reviewer should
balance out the need to make progress against the importance of the changes
they suggest.

Reviewers should feel free to leave comments to the effect that something could
be improved, but could prefix with e.g. "Nit:" to indicate a point of polish
that could be ignored.

This does not mean approving changelists that worsen code health overall, except
in emergency cases.

#### Mentoring

Code review can be used to teach developers something new. Fine to leave
comments that help someone learn. Sharing knowledge is itself part of improving
a system's code health over time.

If solely an educational point, make this clear in comments; for instance, you
could again use "Nit:".

#### Principles

* Technical facts and data override opinions and personal preferences.
* On style matters, defer to the applicable style guide for the organisation.
  Style points that are not in the style guide are personal preference. The
  style should be consistent with the code present already. If no previous
  style, accept the author's.
* Aspects of software design are almost never a pure style issue or just a
  personal preference. Should be weighted on the underlying principles.
  If the author can demonstrate there are several valid approaches (through data
  or based on engineering principles), the reviewer should accept the author's
  choice. Otherwise, defer to standard software design principles.
* If no other rule applies, the reviewer may ask for the changelist to be
  consistent with the current code, as long as it doesn't worsen the overall
  code health.

#### Resolving conflicts

First, try to reach consensus between developer and reviewer. If that's
difficult, try to have a face-to-face meeting or call, and record the results
of the discussion in the code review comments. Otherwise escalate to a suitable
person, e.g. a code maintainer or a manager. Don't let a changelist languish
because there's no agreement.

### What to look for in a code review

Take "The Standard of Code Review" into consideration for each of these points.

#### Design

* Do the interaction of pieces of code in the changelist make sense?
* Does this change belong in this codebase, or in a library?
* Does it integrate well? Is it a good time to add this?

#### Functionality

* Does this changelist do what the developer intended?
* Is what the developer intended good for the code's users? (Both end-users
  and other developers who have to work with the code in future.)

Expect that developers should test changelists well enough to ensure they work
correctly. As a reviewer, should consider edge cases, concurrency problems,
trying to think like a user and check there are no bugs found from reading the
code.

Can validate the changelist. Most important when a user-facing change, e.g. UI
change. Can ask developer to give a demo, if inconvenient to try yourself.

Parallel programming can be tricky to evaluate, may cause deadlocks or race
conditions. Hard to detect by looking at the code. Need developer and/or
reviewer to think changes through. Also good to avoid parallel programming where
deadlocks or race conditions are possible.

#### Complexity

* Is the changelist more complex than it should be? Check this at every level
  of the changelist: individual lines, functions, classes. "Too complex" means
  "can't be understood quickly by code readers" and/or "developers are likely to
  introduce bugs when trying to call or modify this code".
* Over-engineering can make code complex: too generic or additional
  functionality that isn't presently needed. Should be watchful for this; solve
  the problem needed to be solved now, not a speculated problem in future.
  Should solve problems as they arrive and you can evaluate their requirements.

#### Tests

* Request unit, integration or end-to-end tests as appropriate. Tests should
  be added in same changelist as the production code, unless of emergency.
* Tests should be correct, sensible and useful. Tests do not usually have tests
  themselves.
* Will tests fail if code is broken?
* Will tests produce false positives when code changes?
* Does each test makes simple and useful assertions?
* Are the tests separated appropriately?
* Tests are code that have to be maintained. Don't accept complexity in tests.

#### Naming

* Did the developer pick good names for everything? Should communicate what
  something does without being long and difficult to read.

#### Comments

* Are there clear comments in comprehensible English?
* Are all comments necessary?
* Comments should explain why code exists, not how it works. If the code
  isn't self-explanatory, it should be simpler. There are exceptions (e.g.
  regular expressions or complex algorithms), but comments should contain
  information that the code can't contain.
* Look at comments made before this changelist; maybe a TODO can be removed, or
  perhaps there is a comment advising against this change.
* Comments are different from documentation of classes, modules or functions
  which should explain the purpose of a piece of code, how it should be used and
  how it behaves when used.

#### Style

* If using a style guide for code base, check the changelist follows this guide.
* To suggest improvements to small style points not in style guide, prefix with
  "Nit:".
* Major style changes should not be combined with other changes (in same way
  as major refactoring). Makes it difficult to understand the changes, and can
  make merges or rollback difficult.

#### Documentation

* If the changelist changes how users build, test, interact with or release
  code, check to see that associated documentation is updated. If the changelist
  deletes or deprecates code, consider deleting relevant documentation.
* Request documentation if it should be present, but is missing.

#### Every line

Consider every line that is to be reviewed. Some things like data files,
generated code or large data structures may be sometimes scanned over, but don't
scan over something human-written and assume this is OK. Some code requires more
scrutiny than other code, but you should understand what all the code is doing.

If it's too difficult for you to read the code, then let the developer know
that. Others may find it difficult to understand too.

If you understand the code, but don't feel qualified to do some part of the
review, make sure someone who is qualified to review does so, especially for
issues like security, concurrency, accessibility, internationalisation.

#### Context

Often helpful to look at the changelist in context of the code. Look around the
whole file to make sure the change makes sense; maybe there are a few lines
added, but to a large method that now needs to be broken up.

Think about the changelist in the context of the system. Is this improving
the code health or making the system more complex? Most systems become complex
over many small changes that add up, preventing small complexities can help.

#### Good things

If you see something nice, tell the developer. Should offer encouragement
to developer, and can be helpful to tell them what they did right.

#### Summary

* The code is well-designed.
* The functionality is good for the users of the code.
* Any UI changes are sensible and look good.
* Any parallel programming is done safely.
* The code isn’t more complex than it needs to be.
* The developer isn’t implementing things they might need in the future
  but don’t know they need now.
* Code has appropriate unit tests.
* Tests are well-designed.
* The developer used clear names for everything.
* Comments are clear and useful, and explain why the code is there
  instead of what the code does.
* Code is appropriately documented.
* The code conforms to the style guides in use.

### Navigating a changelist in review

#### Summary

Steps:

1. Does the change make sense? Does it have a good description?
2. Look at the most important part of the change overall. Is it well-designed
   overall?
3. Look at the rest of the changelist in an appropriate sequence.

#### Take a broad view of the change

Look at the description and what it does in general. Does the change make sense?
If it shouldn't, then respond with an explanation of why it shouldn't happen.
Also, explain to the developer constructively and courteously what they should
have done instead.

If you get several changelists that represent changes you don't want to make,
consider re-working the team's development process or the process posted for
external contributors. Easier to tell people "no" ahead of spending time working
on something to throw it away or rewrite.

#### Examine the main parts of the changelist

Find the file or files that are the "main" part of this changelist. Often, one
file has the largest number of logical changes, and it's the major piece of the
changelist. Look at these first, to give context to the smaller changes. If too
large to figure out which parts are the major parts, ask the developer what to
look at first, or request breaking into smaller parts.

If you see major design problems with this "main" part, then send those comments
immediately even if no time to review the rest. May be the case that if there
are large enough design issues that a lot of the other code under review may
disappear and not matter. Important because:

* Developers may be basing work on the changelist under review already; they'll
  have to rework this later work too.
* Major changes take longer to do than small changes. Developers often have
  deadlines. To make those deadlines and still have quality code, need to start
  re-work as soon as possible.

#### Look through the rest of the changelist in an appopriate sequence

When confirmed that there are no design problems as a whole, try to figure out
a logical sequence to look through the files while making sure you don't miss
any file. Can be just the order the review tool presents them after considering
the major files.

Sometimes helpful to read the tests first before the main code, to get an idea
of what the change is supposed to do.

### Speed of code reviews

#### Why should code reviews be fast?

Google try to optimise for speed of a developer team producing a product, not
the speed for which an individual developer can write code. The latter is
important, just not as important as the former.

Slow code reviews mean that:

* The velocity of the team as a whole is decreased. An individual not responding
  gets other things done, but other features and bug fixes by the rest of the
  team are delayed.
* Developers get frustrated by the code review process. Complaints about code
  review tend to disappear when the process is quicker.
* Code health can be impacted. With slow reviews, there is increased pressure
  for developers to submit changelists that are not as good as they could be.

#### How fast should code review be?

If not in the middle of a focused task, do a code review shortly after it comes
in. One business day is the maximum it should take. Means that a changelist can
get several rounds of review in a single day if required.

#### Speed versus interruption

Don't interrupt yourself to do a code review. Personal velocity trumps team
velocity here. Can cost more to the team to break off to review, even though
the review gets done quicker. Wait until a break point in your work.

#### Fast responses

For speed of code reviews, concerned with response time, not how long a
changelist takes to get through the whole review. Whole process should be fast,
but important for individual responses to come quickly than the whole process to
be rapid.

If too busy to do a full review on a changelist when requested, can send a quick
response to let the developer know when you will get to it, suggest other
reviewers who might respond more quickly or provide broad comments.

#### Cross-time-zone reviews

Try to get back to the author when they are still in the office. If not, try to
get the review done before they get to the office the next day.

#### LGTM with comments

To speed up code reviews, reviewer should give LGTM/Approval even though they
are leaving unresolved comments on the changelist.

This is done when either:

* The reviewer is confident that the developer will appropriately address all
  the reviewer's remaining comments.
* The remaining changes are minor and don't have to be done by the developer.

The reviewer should specify which of these options they intend.

"LGTM with comments" is helpful when developer and reviewer are in different
time zones.

#### Large changelists

If a code review is large and you don't know when you will have time to review
it, typically you should ask the developer to split the changelist into smaller
ones that build on each other.

If it can't be broken up, and no time to review the whole thing, write some
comments on the overall design of the changelist and send back to the developer
for improvement. As a reviewer, you should aim to unblock the developer or
enable them to take some further action quickly, without sacrificing code
health.

#### Code review improvements over time

If you follow these guidelines and are strict with code reviews, you should find
that the entire process tends to get faster over time. Developers learn what is
required for healthy code and send good changelists from the start, requiring
less review time. Reviewers learn to respond quickly.

Don't compromise on code review standards for an imagined improvement in 

#### Emergencies

Emergencies where changelists must pass the review process quickly may have
relaxed quality guidelines. See below for which situations qualify as
emergencies.

##### What is an emergency?

Sometimes require emergency changelists to pass through code review as quickly
as possible.

An emergency changelist is a small change e.g.:

* that allows a major launch to continue instead of rolling back;
* fixes a bug significantly affecting users in production;
* handles a pressing legal issue;
* closes a major security hole etc.

Care about the speed of the code review process, not just the response. The
reviewer should care more about speed of review and correctness of the code
(does it resolve the emergency?) over anything else.

Emergency reviews should take priority over other code reviews.

Once the emergency is resolved, should look over the emergency changelist
again and review thoroughly as you would usually.

##### What is not an emergency?

Examples:
* Wanting to launch this week instead of next week (unless there is a hard
  deadline for launch, such as a partner agreement).
* The developer has worked on a feature for a long time and really want to get
  the changelist in.
* The reviewers are all away or in another timezone.
* It is the end of Friday and would like to get this changelist in before the
  weekend.
* A manager says the review has to be complete and the changelist checked in
  because of a soft deadline.
* Rolling back a changelist that is causing test failures or build breakages.

##### What is a hard deadline?

A deadline where something disastrous would happen if missed, e.g.

* Submitting a changelist by a date is required for a contractual obligation.
* Your product will fail in the marketplace if not released by a certain date.
* Some hardware manufacturers only ship new hardware once a year. Missing the
  deadline to submit code to them could be disastrous, depending on the type
  of code you're shipping.

Delaying a release for a week is not disastrous. Missing an important
conference might be disastrous, but is not often.

Most deadlines are soft, not hard; often represent a desire for a feature to be
done by a certain time. They are important but should not sacrifice code health
to meet them.

For long release cycles (several weeks) can be tempted to get a feature in 
before the next cycle, and sacrifice code review quality. This can build up
technical debt. If developers are routinely submitting change lists near the
end of release cycles that "must" get in with superficial review, the team
should modify its process so large feature changes happen early in the cycle
and with enough time for good review.

### How to write code review comments

#### Summary

* Be kind.
* Explain your reasoning.
* Balance giving explicit directions with just pointing out problems and 
  letting the developer decide.
* Encourage developers to simplify code or add code comments instead of just
  explaining the complexity to you.

#### Courtesy

Important to be courteous and respectful while being clear and helpful to the
developer whose code is under review.

Try to make comments about the *code*, not the *developer*, especially when
saying something that might be otherwise upsetting or contentious.

Bad: "Why did *you* use threads here when there’s obviously no benefit to be
gained from concurrency?"

Good: "The concurrency model here is adding complexity to the system without any
actual performance benefit that I can see. Because there’s no performance
benefit, it’s best for this code to be single-threaded instead of using multiple
threads."

#### Explain why

The "good" example above helps the developer understand your comment. Don't
always need to include exposition, but can be appropriate to give more detail
about your intent, the best practice you're following, or how your suggestion
improves code health.

#### Giving guidance

In general, it is the developer's responsibility to fix a changelist, not the
reviewers. Not required to do detailed design of a solution or write code.

Doesn't mean reviewer should be unhelpful. Try to strike an appropriate balance between pointing out problems and giving direct guidance. Pointing out problems
and letting developer decide can help the developer learn, and make code reviews
easier. Can also result in a better solution, as developer is closer to the code
than the reviewer.

Sometimes suggestions, direct instructions or code are more helpful. The main
goal of code review is to get the best changelist possible. A secondary goal is
helping developers improve so they require less review over time.

#### Accepting explanations

If you ask a developer to explain code that you don't understand, it should
usually result in them rewriting the code more clearly. Adding a comment is
occasionally an appropriate response, providing the comment is not just
explaining overly complex code.

Explanations written only in the code review tool are not helpful to future
readers. This should only be in occasional cases, e.g. the reviewer is not
familiar with the area the code deals with, and the developer explains something
that normal readers of the code would have known.

### Handling pushback in code reviews

Sometimes a developer will push back on a code review, either disagreeing with
your suggestion or complaining you are being too strict.

#### Who is right?

If a developer disagrees with your suggestion, consider whether they are
correct. They are often closer to the code than a reviewer and maybe have a
better insight. If their argument makes sense, then let them know they are right
and drop the issue.

Developers are not always right. Here, the reviewer should explain further why
they believe that their suggestion is correct. A good explanation both shows an
understanding of the developer's reply and additional information about why the
change is being requested.

In particular, when the reviewer believes their suggestion will improve code
health, they should continue to advocate for the change, if they believe the
resulting code quality improvement justifies the additional work. Improving code
health tends to happen in small steps.

Sometimes it takes a few rounds of explaining a suggestion before it sinks in.
Stay polite and let the developer know that you hear what they're saying, you
just disagree.

#### Upsetting developers

Reviewers sometimes believe that the developer will be upset if the reviewer
suggests an improvement. Sometimes developers do become upset, but often brief
and they become thankful that you helped them improve the quality of their code.

If you are polite in your comments, developers usually don't become upset at
all. Upsets are usually more about how comments are written, than an insistence
on code quality by the reviewer.

#### Cleaning it up later

A common source of push back is that developers want to get things done. They
don't want to go through another round of review. So they say they will clean
something up in a later changelist and this one should therefore be approved
now. Some developers are good about this and will immediately do so.

However, the more time after the original changelist, the less likely the clean
up will happen. Usually, unless the developer does this clean up immediately
after the present changelist, it doesn't happen. Developers are busy and this
cleaning gets forgotten about.

Best to insist it is tidied up *now* before the code is "done".

If a changelist introduces new complexity, it must be cleaned up before
submission unless an emergency. If the changelist exposes surrounding problems
and they can't be addressed right now, the developer should file a bug for the
clean up and assign it to themselves. They can optionally write a TODO
comment in the code that references the bug.

#### General complaints about strictness

If you have previously had lax code reviews and switch to strict reviews, some
developers will complain very loudly. Improving the speed can make these
complaints fade away. May take time for complaints to fade away, but eventually
developers tend to see the value of strict code reviews as they see the good
code they help generate.

#### Resolving conflicts

See previous section on resolving conflicts.

## Guidance for authors of code changes

### Write clear changelist descriptions

Description of a changelist is important: for future reference. May be searched
for, by other people.

Explain why the changes are necessary; first line, explain *what* it does
and/or the change from the past; rest of description, talk about implementation,
the context, future work; explain *why*.

Review the changelist description yourself before submitting the PR for review.

### Keep changelists small and simple

Small changelists are:

* **Quicker to review**, as smaller.
* **More thoroughly reviewed**, as simpler.
* **Less likely to introduce bugs**, as easier to reason about.
* **Less wasted work if rejected**.
* **Easier to merge**, as fewer potential conflicts.
* **Easier to design well**, as less to refine.
* **Less blocking on review**, as breaking apart a large overall change into
  small, self-contained changes, means you can work on those while waiting for
  the current review.
* **Simpler to roll back**, if rolling back, likely to be fewer files that may
  have also been changed in the interim.

#### What is small?

* One self-contained change: often one part of a feature, not an entire
  feature.
* 100 line changes are usually OK; 1000 lines too large, but up to reviewer.
* Number of files affected may also be a factor; 200 lines in one file may be
  OK, but 200 lines in 50 files may be too large.

Exceptions:

* Large changes caused by deleting files, as that can be considered as one line
  of change.
* Changes caused by some automated refactoring tool that you trust.

If it doesn't seem like you can write a small changelist, consider:

* Can you submit a refactoring-only changelist first?
* Ask others if they have ideas.
* Discuss with reviewers before writing, taking into consideration that large
  changelists are more difficult to work with, as the above arguments.

#### Splitting by file

* May group a changelist by files affected that require different reviewers, but
  are otherwise self-contained changes, e.g. one changelist for a code change,
  and one for the configuration that uses that code.

#### Separate out refactorings

* Keeps changelists simpler as not mixing refactoring with new features or bug
  fixes.
* Use judgement: small tidying such as to a local variable name might be OK.

#### Keep related test code in the same changelist

* Keep validation of your code modification together with those modifications in
  a changelist.
* Independent test modifications can go into separate changelists, e.g. new
  test for existing code; refactoring tests, introducing larger test framework
  code (e.g. integration test).

#### Don't break the build

* If you have several changelists that are dependent on each other, need to
  ensure the whole system keeps working after each changelist is submitted.

### Handling reviewer comments

#### Don't take it personally

* A critique on code isn't a personal attack.
* Sometimes reviewers may express frustration in their comments. This isn't good
  practice for reviewers, but as the submitter you should try and look past that
  to whatever is the constructive idea they are trying to communicate.
* Never respond angrily; calm down, walk away if needed, and then reply.
* If a reviewer isn't providing helpful or polite feedback, explaining in person
  to them is best. Escalate this to a manager if you continue to have problems.

#### Fix the code

* Clarify the code if a reviewer doesn't understand something. If that's not
  possible, add a comment. If a comment seems unnecessary, add a comment in the
  code review tool.
* Comments in code review tool may not help future readers of the code; they may
  not have access to that tool, or the comments may be lost in migration to
  other tools. Improvements to code or comments there can help (reader's note:
  also clarifications in commit descriptions can help too).

#### Think for yourself

* A changelist can be considerable work. May think that at that stage, it's
  complete and no further work is needed. However, if you get review comments,
  consider whether the reviewer is correct. It may be that the reviewer needs
  to clarify their comments. It may be that, on reflection, you still think
  you're right: provide the reviewer with a response of why these changes
  improve things for the codebase, users and/or the organisation developing the
  code.

#### Resolving conflicts

* Try to reach a consensus with reviewer. Otherwise, see the below section for
  reviewers.
