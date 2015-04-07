spinalstack-doc
===============

Official manual of Spinal Stack

Contributing
------------

You should **not** send pull-requests through GitHub and rather use our
Software Factory's Gerrit instance.

1. Install `git-review` with apt, yum or pip

2. Login to [Software Factory](http://softwarefactory.enovance.com/) with your
   github account

3. Set-up gerrit in the project if it's the first time you're contributing to
   the project: with `git review -s`

4. Make your changes using the standard git workflow, commit in the end. (Note:
   It's better to make your changes in a feature branch `git checkout -b
   <feature>` and use one commit per review)

5. Submit your changes using `git review` and follow the link to see the reviews.

6. To propose another change, change back to the `master` branch and go back to
   step 4. You can also create a change linked to you latest change by staying
   on this change's branch but the new change(s) will depend on the old change
   and won't be merged until the first change has been merged.
