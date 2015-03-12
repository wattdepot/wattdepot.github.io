---
layout: documentation
title: Release process
---
# Release process

The release process consists of several steps.

## Entry conditions
Before starting the release process the `develop` branch must be 
ready for release, all unit tests, checkstyle, findbugs, pmd, etc. pass 
[TravisCI](https://travis-ci.org/).


## Step 1 Create release branch in Github and merge it into the master branch.

1. Create a release branch from the `develop` branch. Release branches support preparation of a 
new production release. They allow for last-minute dotting of i's and crossing t's. Furthermore,
they allow for minor bug fixes and preparing meta-data for a release (version number, build dates, 
etc.). By doing all of this work on a release branch, the develop branch is cleared to receive 
features for the next big release. 

    $ git checkout -b release-3.0.1 develop

2. Update the version number in the `pom.xml` file, minor bug fixes, meta-data, etc.  Ensure that the release branch 
passes all unit tests, checkstyle, findbugs, pmd, etc. Don't forget to update the `Procfile` file to point to the
new wattdepot-*version number*.jar file.

3. Commit all the changes to the release branch.

    $ git commit -a -m "Bumped version to 3.0.1."
    
4. Switch to the `master` branch.

    $ git checkout master

5. Merge the release branch into `master`.

    $ git merge --no-ff release-3.0.1

6. Ensure that the `master` branch passes all unit tests, checkstyle, findbugs, pmd, etc.

    $ mvn package site

7. (Optional) Delete the old release branch.

    $ git branch -d release-3.0.1

8. Push the changes to *Github*.

    $ git push origin master
    
9. Check that continuous integration passes [TravisCI](https://travis-ci.org/).
  
## Step 2 Upload the Release's Maven `site` directory to GitHub.
    
1. Move the `target/site` to a safe place (e.g. Your home directory).

2. Checkout the `gh-pages` branch.
    
3. Replace the old `site` directory with the updated `target/site` directory you saved.

6. Add all the changes to the `gh-branch`.
    
    $ git commit -a -m "Javadoc for 3.0.1"
        
7. Push the changes to the `gh-pages` branch.
    
    $ git push origin gh-pages.
        
8. Switch back the `master` branch.
    
    $ git checkout master 
    
## Step 3 Create a GitHub release.

1. Create the three WattDepot release files by running the following command.

{% highlight bash %}
$ mvn package -P rel
{% endhighlight %}

  This will create WattDepot-&lt;version&gt;.tar.bz2, WattDepot-&lt;version&gt;.tar.gz and WattDepot-&lt;version&gt;.zip files in the `target` directory.

2. Go to the [WattDepot GitHub page](https://github.com/wattdepot/wattdepot).

3. Click on the `release(s)` link in the toolbar.

4. On the Releases page click the `Draft a new release` button.

5. Fill out the Tag version input field with the version of the release (e.g. 3.0.1) on branch 
`master`.

6. Fill in the Release title (e.g. 'Release 3.0.1').

7. Fill in the description of the release.

8. Drag the WattDepot-&lt;version&gt;.tar.bz2, WattDepot-&lt;version&gt;.tar.gz and WattDepot-&lt;version&gt;.zip files to the "Attach binaries for this release by dropping them here." box.

9. Click the "This is a pre-release" checkbox if it is.

10. Click the "Publish release" button.