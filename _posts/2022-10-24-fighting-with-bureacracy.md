---
layout: post
title: "Fighting with bureaucracy"
date: 2022-10-01 10:00:00 +0400
categories: cli
---

# Fighting with bureaucracy through terminal

## Motivation

I was an opponent of such posts due to they seemingly obviousness. Everyone already know that, right? Why should I write another compilation of README that offer basically nothing new? 

That was my line of thoughts before I start casually sharing knowledge in conversation with coworkers. Turns out the thing you considers obvious doesn't seems obvious to another (duh).

Okay, I'm done with excuses, it's already took to long.  

## Jira

> This is probably most YMMV part of text. Everyone has there own workflows. I share mine, hope you find something useful.  

Context switches annoys me. They are source of wasted time. Modern web takes too much time to load and my attention span is getting worse every year. 

I want to create ticket, switch to feature branch and start working. Not much, huh? You probably need to enter one string to properly name a ticket (no description for a start, title should be suffice), Jira issue key to name a feature branch and you done with corporation bullshit.

All of that should be easily accomplish in a single command like `git commit . -m "Blah blah blah"`. And it's actually possible with the help of [jira-cli](https://github.com/ankitpokhrel/jira-cli). On the time of writing it's in WIP state, but already has essential features. 

After installation and first configuration (typing `jira init` and following wizard steps) I add some aliases and zsh function for productivity: 

```zsh
alias jil='jira issue list -a$(jira me) --order-by status --reverse -s~Closed --paginate 10'
alias jic="jira issue create"
alias jicb="jira issue create -tBug "
alias jsw="jira_start_work"

jira_start_work () {
	jira issue assign $1 $(jira me)
	jira issue move $1 "In Progress"
}
```

Here a description: 
- `jil` — browsing current issues ("Open", "In Progress" and "In Review"). Default pagination setting is 50 items and network request takes too much time to finish, so, I decrease value to 10. Also add filter for closed tasks (you probably have your own type of Jira Issues) and sorting by status.
- `jic` & `jicb`— create issue and bug ticket.   
- `jsw` — shortcut for calling `jira_start_work()` function that assign and move "In progress" passed Jira issue. Used this on newly created issues.  

So, typical workflow for me look like this: 

```shell 
# decide to work on something, maybe small feature/refactor/bugfix 
$ jic -s "Fix CI: artifact link lead to Rick'Roll"
# print jira issue key and link to it 
$ git checkout -b <Jira Issue Key>
$ jsw <Jira Issue Key>
```

And everything is done, no need to open browser. Saved some precious time and nerve cells.

Note: [jira-cli](https://github.com/ankitpokhrel/jira-cli) has [huge collection of snippets](https://github.com/ankitpokhrel/jira-cli#commands), you should definitely check it.  

## GitLab 

Here comes the second part of equation. 

After work is done I need (and you most likely too) to submit changes to remote repository e.g. open Merge Request (that's how GitLab people says Pull Request, because in Software Development we love innovation and hate standards).

GitLab is better than Jira in terms of loading (not the highest bar, let's be honest), but still requires much more time than it should. How to mitigate that problem?

Use [glab](https://gitlab.com/gitlab-org/cli) (if you are on GitHub you can use [gh](https://github.com/cli/cli/) they are interchangeable in my experience) and add some aliases: 

```zsh
# glab shortcuts
alias gmc="glab mr create -a <my_username> --reviewer=<teammates>" 
alias gmv="glab mr view --web"
```

Here the description: 
- `gmc` — create MR, assign it to me and add my favorite coworkers in review section.
- `gmv` — open merge request in browser (traitor).

I'm use it like this:

```zsh 
# commit finished work  
$ gmc -t "Remove april fools prank (Rick'Roll link)" 
```

You will followed with friendly prompt to add description with your favorite editor (you can skip that part by adding `-d <description>` to command) and conformation button to submit issue.

CI/CD is a part of daily routine, checks must be finished before merge. Some subcommands exists in [glab](https://gitlab.com/gitlab-org/cli/) to help monitoring CI status (`glab ci list` or `glab ci view`), but unfortunately not mature enough (parent-child pipeline support, I'm looking at you). Alias `gmv` exist to mitigate this problem. No need to search or keeping tab to opened MR. Hopefully this changes in the future.