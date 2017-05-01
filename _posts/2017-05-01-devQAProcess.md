---
layout: post
title: How we refined our Developer/Tester relationship into an efficient process
tags: agile

---

## Our current process, ensuring the Developers and Tester make the most of each other's time

Over the course of many retrospectives, we have made bit-by-bit changes to our processes, chipping away at small problems as they appear until we've reached what I feel is a very good working relationship that makes everyone's lives easier. These are the main problems we wanted to solve or objectives we wanted to achieve:

  - **Regular end-of-sprint releases**; Although we were agile in all other aspects of development, we were often working on a feature for 6 months and not releasing until the end of that feature. This caused many problems: merge conflicts for both Test and Dev were rife since the master branch was always moving forward even if we'd not many any changes. A large big-bang release is also very scary since there are months of features to suddenly worry about, from feedback from alpha clients to conflicts with other large features which are being released at the same time. 
  - **Efficient use of Testers**; Our QA-to-Dev ratio is the department's main weakness; probably about 7 to 1. Until new members are recruited and new recruits trained, we have to ensure that Testers are only testing work that is ready for them.
  - **Supporting our Product Owner**; We don't expect our product owner to be too technical, but we do expect them to be getting feedback on our product as it stands that day from clients, other areas of the business or giving feedback themselves, and a leadtime for a Tester or Dev to set up a VM and populate it with data makes it a cumbersome process, and no one wants to have to regularly ask for help and feel like a pest just to do their job, especially as often we've resorted to using ngrok on the Tester's machine which locks it for testing.

Other annoyances and gripes contributed to the process we came up with and are now comfortable with. Obviously this won't work with all teams, the point of the retrospectives is that the new process works _for us at the moment_, meaning that it won't be just right out-of-the-box for other teams in the company nor will it always work for us, especially when we lose or gain team members.

# Our process / Requirements

1. A story must be refined and agreed on by the Team. It must be small, testable and deliverable. A story should be broken up into smaller releasable chunks wherever possible; for example splitting up front-end and backend components.
2. For a new feature, an enablement flag is created and all code guarded by it; If we are refactoring an existing page we'll create a whole new page and hide it behind the feature flag. This means that Stakeholders or alpha clients can be given the new page, while also being able to use the old one should features be missing or unforeseen bugs preventing full usage. This makes regular sprint releases possible, and it as simple as a ClientID/FeatureID pivot table in a database.
3. Once the stories are ready, they are brought into the sprint with all the team members workflow in mind and prioritised according to who needs what first. For example, in an API endpoint using full CRUD, CREATE will be the first method implemented. This helps Testers create test data for later methods. Fake endpoints are created with GET data for the front end to begin their work. If the front end is created with no endpoint, or DELETE is created for CREATE, that just means a task will be sitting untested.
4. Before an API endpoint is implemented, a branch of the public API documentation is created and the documented added. We have found that this causes the QA and Devs to talk and put the documentation through multiple revisions, from various "But what if.." questions to "We don't put actions in the URI we use HTTP methods!". Very much the kind of thing you want to catch earlier. The main benefits:
   * it helps the Developers map out an endpoint before they even touch their IDE. So many prefer to just hammer out code straight away and the simple act of writing documentation tells them what they do and don't know or understand about it.
   * I test _completely_ against the API documentation. A client won't be able to tap a Developer on the shoulder. If I don't have enough information to use that API, then the documentation needs to be improved. 
5. A master branch is created for the sprint. This is a release candidate, and nothing will be added to this until it is fully tested by the Tester and reviewed by the Product Owner. The master branch will be added to a staging server for the PO. Test will keep it populated with data, and any Developer who merges their tested feature into the sprint master will do a pull on the staging server so the PO always has a snapshot of the state of the release. Individual tasks or stories will have their own branches, once these branches are delivered to the QA swimlane a developer will never touch them again unless a bug is reported. Why would a developer touch something that is supposedly ready for test?
6. Using this documentation, The Tester can being writing API automated tests. Rather than an endpoint be giving to a Tester then the tests being written, both can be done in parallel. This means the lead-time to test is tiny. As soon as the endpoint is finished the Tester is running their manual (First automation run) against it. Anything that passes gets added to the expected response JSON, anything that fails is reported. This is then added to the Jenkins regression suite (As are front-end tests, though these are written _after_ the task is complete)
7. Developers run their tasks through the Jenkins regression suite before giving it to Test. This is part of our "Definition of ready for Test" (As is th edevelopment branch it lives on being provided with the ticket). This saves a **lot** of time. I've seen developers either introduce regression bugs from ancient endpoints or the new tests written in point (6), and spend 2 days constantly running tests until it works again. This is 2 days out of a 10 day sprint that Testers aren't wasting. Our Jenkins suite can run full API and Selenium tests over 6 years of code within an hour, so the developers get faster feedback. This means that:
   * When Testers see a bit of code, it's to try to make up strange edge cases mostly; a page not even loading or an endpoint that affects something we did a year ago won't even make it to us.
   * Anecdotally, this alone increases efficiency so much that This month I spent 2 weeks working in our Tokyo office, a city 8 hours ahead of our UK office. During the day I could write various tests and the leave work. When the rest of the team got into the office in the UK, they were happily running my tests without needing me to even be there. As our team's only Tester it was really a testament to how streamline we are.
8. When a task passes, it gets moved to a "Tested" swimlane. It is held here until it is merged into master. All too often we've written a micro-feature for a client alongside our main work, tested it, all within a few days, then 6 weeks later the client asks where it is. This is because anything in the "Done" swimlane may as well be forgotten. Another sparkling advantage of a release branch, no keeping track! Once it is merged to master that development branch is deleted, and again the Developer doing the merge does a pull on the PO's staging environment.
9. When the sprint is over, whether all tasks are finished or not, and whether some of the tasks have bugs or not, the sprint release candidate is always ready for release, so we release it to master and push the automated tests to the QA master. This means that developers from other teams will be testing against our features in their development cycle even though they are turned off with a feature flag!


All of this needs a team that works well together. I wouldn't recommend a big-bang introduction of any of this, but rather incrementally as we developed it. This looks like a large and formal list but it was developed in such tiny changes over many retrospective that it's second-nature to everyone in the team. We did have a Developer join from another team using a more wagile approach, who was inititally sceptical at our way of doing things which can look convuluted all at once, but when he saw releases going live every sprint so you never have to worry about merge conflicts or large swathes of code suddenly hitting live at the same time, when other teams are testing against your half-finished features already and you can forget everything at 5pm without worrying about what firefighting you can be doing the day after the big release, they suddenly become **very** enthusiastic!

A few of the Developers and I have presented this process to a few other teams within the company so they're currently cherry-picking elements they like and either scrapping or tweaking bits that they feel wouldn't work for them, which is exactly how I think it should be adopted, and I'm excited to see how this helps.

