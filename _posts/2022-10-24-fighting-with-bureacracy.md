---
layout: post
title: "Fighting with bureaucracy"
date: 2022-10-01 10:00:00 +0400
categories: cli
---

# Fighting with bureaucracy through terminal

## Motivation

I was an opponent of such posts due to their seeming obviousness. Everyone already knows that, right? Why should I write another compilation of README's that offers basically nothing new? 

That was my line of thoughts before I started casually sharing knowledge in conversations with coworkers. Turns out the thing you consider obvious doesn't seem obvious to others (duh).

Okay, I'm done with excuses, it already took too long.  

## Jira

> This is probably the most YMMV part of text. Everyone has their own workflows. I share mine, hope you'll find something useful.

Context switches annoy me. They are a source of wasted time. Modern web takes too much time to load and my attention span is getting worse every year. 

Say, I want to create ticket, switch to a feature branch and start working. Not much, huh? You probably need to write one string to properly name a ticket (no description for a starters, a title should be enough), Jira Issue Key to name a feature branch and you are done with corporate bullshit.

All of that should be easily accomplished in a single command like `git commit . -m "Blah blah blah"`. And it's actually possible with the help of [jira-cli](https://github.com/ankitpokhrel/jira-cli). At the time of writing it's in WIP state, but already has all essential features<sup>[citation needed]</sup>. 

After installation and first configuration (typing `jira init` and following wizard steps) I add some aliases and zsh function for productivity: 

```zsh
alias jil="jira issue list -a$(jira me) --order-by status --reverse -s~Closed --paginate 10"
alias jic="jira issue create"
alias jicb="jira issue create -tBug"
alias jsw="jira_start_work"

jira_start_work () {
	jira issue assign $1 $(jira me)
	jira issue move $1 "In Progress"
}
```

Here's what they do: 
- `jil` — browse current issues ("Open", "In Progress" and "In Review"). Default pagination setting is 50 items and network request takes too much time to finish, so, I decreased the value to 10. I also added a filter for closed tasks (you probably have your own type of Jira Issues) and sorting by status.
- `jic` & `jicb`— create issue and bug ticket.   
- `jsw` — shortcut for calling `jira_start_work()` function that assigns the task to my profile and moves it to "In progress" state. The function takes Jira Issue Key as an argument. Used this on newly created issues.  

So, typical workflow for me looks like this: 

```shell 
# decide to work on something, maybe small feature/refactor/bugfix 
$ jic -s "Fix CI: artifact link lead to Rick'Roll"
# print Jira Issue Key and link to it 
$ git checkout -b <Jira Issue Key>
$ jsw <Jira Issue Key>
```

And everything is done, no need to open browser. Saved some precious time and nerve cells.

Note: [jira-cli](https://github.com/ankitpokhrel/jira-cli) has a [huge collection of snippets](https://github.com/ankitpokhrel/jira-cli#commands), you should definitely check it out.  

## GitLab 

Here comes the second part of the equation. 

After the work is done I need (and you most likely too) to submit changes to remote repository, e.g. open a Merge Request (that's how GitLab people say Pull Request, because in Software Development we love innovation and hate standards).

GitLab is better than Jira in terms of loading speed (not the highest bar, let's be honest), but still requires much more time than it should. How can we mitigate that problem?

Use [glab](https://gitlab.com/gitlab-org/cli) (if you are on GitHub you can use [gh](https://github.com/cli/cli/), they are interchangeable in my experience) and add some aliases: 

```zsh
# glab shortcuts
alias gmc="glab mr create -a <my_username> --reviewer=<teammates>" 
alias gmv="glab mr view --web"
```

Here's what they do: 
- `gmc` — create Merge Request, assign it to me and add my favorite coworkers in review section.
- `gmv` — open merge request in browser (traitor).

I use it like this:

```zsh 
# commit finished work  
$ gmc -t "Remove april fools prank (Rick'Roll link)" 
```

A friendly prompt will follow asking you to add a description with your favorite editor (you can skip that part by adding `-d <description>` to the command) and a conformation button to submit issue.

CI/CD is a part of daily routine: checks must be finished before the merge button become available. Some subcommands exist in [glab](https://gitlab.com/gitlab-org/cli/) to help monitoring CI status (`glab ci list` or `glab ci view`), but unfortunately they're not mature enough (parent-child pipeline support, I'm looking at you). The alias `gmv` exists to mitigate this problem. No need to search or keep the tab with opened Merge Request. Hopefully this changes in the future.