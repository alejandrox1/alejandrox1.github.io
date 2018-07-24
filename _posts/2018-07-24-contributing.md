Hello, world! This is my first blog post (in this blog). I want to use this markdown file to share with you some life-changing advice I have gotten from the tech community: advice on how to make software development easier, more organized, and more pleasant for your team.

Imagine you have a project whose source is sitting on a github repo. I am hoping that you want to make the development on this project easy, comfortable, and fast for all members of your team. This involves putting some thought into the development environment, being explicit about any and all assumptions, and trying to homogenize the workflow as much as possible. Equally important is the process of quality control: we have to make sure that whatever code we are shipping will behave in a certain way and to make sure that when it fails it fails just the way we want it to. Remove as many surprises as we can. 
                                                                                
How many of you (us) have worked in teams/organizations where projects are not very well organized? Engineering decisions are taken in slack channels or emails. Roadmap and team communication live in project management dashboards (i.e., asana). Bugs are silently mention during one-on-one meetings or over slack. Documentation, including code comments, is missing, etc.

For most of us, this happens over and over. We see other projects (props to open source for teaching) and our code base is nothing compared to those; we complain but at the end of the day it works and people get use to it.

We are only briefly reminded of these problems when new people come and reach out to us because they want to contribute to the project or when new hires are assigned to projects. Their learning cure is rough and very steep but there is always someone somewhere encouraging a team to forget about quality and just push it over the fence.

Code is disorganized or, even worse, arbitrarily organized and with no one to atest why it looks the way it does. Bugs are undocumented and everyone is cursed to rediscovering them over and over only to be DM’d to a couple overworked developers who may not get around to them. Something rather common is having multiple teams working with a common code base and everyone stepping into each other’s toes (isn’t version control supposed to take care of this?).

To make matters worse, forget new hires and think about yourself in a couple months. Do you remember that one technical decision you made during a meeting six months ago, the one decision you barely discussed, create an issue for, and didn’t document? Well, it will make your build fail nex week when you are planning on a live demo! And I almost forgot to mention, you will find a really complicated bug tomorrow. It will be so complicated that you will have to rewrite a handful of files. During this rewriting you will mistakenly rewrite a method in your source and you found this problem after a bunch of poorly organized and documented commits. You better start rethinking that part of the project because doing a simple git reset is not going to work.

So let’s get on with the content of this post, shall we?

My comments are inspired, in large part, from the following sources (you should read/watch them):
* [CppCon 2014: Titus Winters "The Philosophy of Google's C++ Code"](https://www.youtube.com/watch?v=NOCElcMcFik)

* [Moby project contributing guidelines](https://github.com/moby/moby/blob/master/CONTRIBUTING.md)

* [Google style guides](https://github.com/google/styleguide)


There are some overall good tips for building software such as always striving for simplicity (really, leave any type of optimization or generalization for the end) and setting up your project such that building and testing is seamless (I know you were wondering why people use Makefiles).
Some other ideas are less adopted, these are the one I want to bring up to your attention.


### The repo should be the source of all truth!

Found a bug? Create an issue. Refactored code/new feature? Create an issue and then show me your PR. Deciding where this project should go? Create an issue and whenever we come to an understanding document the final decision on our ROADMAP.md. You get the idea, yes?

It’s an amazing thing when you don’t have to be looking all over the internet for information!


### Write good commits and keep a clean commit history

Development is hard and messy, but keep that mess away from the repo! The commit history of a good project should read like a book. It should be clear what each commit or pr adds. The commit history should tell you why certain decisions where taken, what contexts where they taken on, and provide you with a concise direction of where to go.
For more in detail discussion see [Keeping commit histories clean](https://www.notion.so/Keeping-Commit-Histories-Clean-0f717c4e802c4a0ebd852cf9337ce5d2), [How (and why!) to keep your commit history clean](https://about.gitlab.com/2018/06/07/keeping-git-commit-history-clean/) and [Using git to prepare your PR to have a clean commit history](https://github.com/mockito/mockito/wiki/Using-git-to-prepare-your-PR-to-have-a-clean-history).

A big part of keeping a clean history is about knowing how to squash commits. 
For example, let’s say you have an open PR you want to merge. 
All you have to do is go on the page, where you see the green button that says
“merge pull request,” click on the arrow and chose “squash and merge” or 
"rebase and merge."

A good rule of thumb for choosing whether to squash or rebase before merging is this: the new commits on your feature branch, do they represent one or multiple independent units of work?
If the changes to your feature branch represent one independent unit of work then “squash and merge,” else, rebase before your feature branch to reflect how many units of work you are adding to the code base.


**Squash and merge**
Using the command line, you can recreate the squash and merge" workflow by 
using the following steps (remember that merged pull request tend to have at 
the end of the commit title the number of the pr enclosed in parenthesis):
```shell
# Go to the branch to which you want to add your changes to.
git checkout master

# squash and merge
git merge --squash <feature branch>

# Close PR #10.
git commit -s -m “Do something (#10)”
``` 

**Rebase and merge**
In this case, the idea is to essentially choose a couple commits from your
feature branch. 
Let's say you have a series of commits you want to add from your feature
branch, commits in the range `X` to `Y` (including `X` and 'Y`).
In order for you to add these commits, do the following:
```shell
# Go to the branch you want to add your changes to.
git checkout master

# Get the SHA of the most recent commit.
git log -n 1

git rebase --onto <SHA most recent> <commit before X> Y

git rebase HEAD master # In this case.
```

### Containerize your project

“It works on my computer.” Have you ever heard that one? Because I have and honestly I might have said it a couple times (I’ll blame it on sporadic cases of the Mondays). Keeping track of all your dependencies is a great thing; being able to test your application in various platforms is wonderful so how about we put that repo of yours inside a container?

Sometime ago, I started messing around with the idea of doing development inside a container. To this end, I actually built this: [github.com/alejandrox1/cool_go/desktop-apps/git_dev](https://github.com/alejandrox1/cool_go/tree/master/containers/desktop-apps/git_dev). On that repo you’ll see a [`Dockerfile`](https://github.com/alejandrox1/cool_go/blob/master/containers/desktop-apps/git_dev/Dockerfile) and a [`run.sh`](https://github.com/alejandrox1/cool_go/blob/master/containers/desktop-apps/git_dev/run.sh) script. The idea is to go inside `run.sh` and specify the url for a repo and a name for the resulting container. `run.sh -b` will create a Docker container with gcc compilers, vim, and a [`.bash_prompt` and `.vimrc`](https://github.com/alejandrox1/cool_go/blob/master/containers/desktop-apps/git_dev/Dockerfile#L29) that I’m in love with (the bash prompt is based of [Jess’s bash_promp](https://github.com/jessfraz/dotfiles/blob/master/.bash_prompt)).
After this, you’ll land inside a container with your repo’s source.

Well, there are some ways this can be done better: instead of using the container to mimic your machine, it would be better to use the container to build and run tests. This way you don’t need to install vim or any other text editor. Also, keeping the code up to date inside a container can be tedious as you’ll have to use the `--no-cache` flag each time you build in order to get the latest code from the repo. This may cause problems if you don’t push all changes upstream before running the container again.

The solution to this last step is to copy (mount to make sure) the path to your local copy of the repository.
Don’t install vim, don’t do any `git clone` inside the container or in the Dockerfile. 
Simply do something like
```
COPY my-badass-repo /go/src/github.com/alejandrox1/my-badass-repo 
```

and to make sure you always have the latest inside your container, mount your repo as a volume:
```
docker run -v my-badass-repo:/go/src/github.com/alejandrox1/my-badass-repo container
```

In case you want to see a cool example of this, here is the [Dockerfile for project Moby](https://github.com/moby/moby/blob/master/Dockerfile), and the documentation describing in detail how to [work with a development container](https://github.com/moby/moby/blob/master/docs/contributing/set-up-dev-env.md).

That’s it for today. Please send me any tips, comments, or suggestions you may have and I hope you found this a little bit useful.

