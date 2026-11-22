I opened my first pull request on a practice software project, proposing a fix for a bug where a login check crashed instead of safely saying "wrong password". If a stored password record was damaged, the app showed the user a server error rather than a normal rejection.

I reproduced the problem, wrote a plan, and built a small fix with tests. Before asking anyone to review it, I ran my own AI-based review tools on it. They caught two things I had missed: an unusual case involving extremely long passwords, and the label on my change, which didn't follow the project's required format. I corrected both.

Where it stands today: the pull request is open, all five of the project's automated checks pass, and it is waiting for a human reviewer. I haven't been told it will be accepted, so I'm describing it as a fix I opened, not one that's finished.

What it taught me: read a project's rules before you start, and get a second set of eyes on your work before a person has to spend time on it.

I drafted this post with help from an AI assistant and edited it myself.

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/223

#OpenSource #Python #SoftwareTesting
