Documentation Documentation
===========================

Doc Doc - Goose?
----------------

Here is some documentation about how this documentation is written. Feel free to pitch-in and improve things.

  1. Markdown - Much of this site is written in the [Markdown format](http://daringfireball.net/projects/markdown/syntax).

    Markdown source files are located in: [src/site/markdown](https://github.com/ActiveStack/active-parent/tree/master/src/site/markdown).
    
  3. You may also find some `README.md` files scattered around the source tree as well.

Site Generation - You can build a local copy of the site for review while editing using this command:

    $ ./mvnw clean site    
The resulting site will be located in: `./target/site/index.html`

Doc Publishing
--------------

To deploy these docs to GH Pages, we use [maven-scm-publish-plugin](https://maven.apache.org/plugins/maven-scm-publish-plugin/)

 1. (One time setup) - <a name="createBranchGHPages">create a `gh-pages` branch</a> and push to github.
    
        $ git push
    
    or

        $ git push --set-upstream origin master
    
    then
    
        $ git pull
        $ git symbolic-ref HEAD refs/heads/gh-pages
        $ rm .git/index
        $ git clean -fdx
        $ echo "My GitHub Page" > index.html
        $ git add .
        $ git commit -a -m "First pages commit"
        $ git push origin gh-pages
        $ git checkout master
        
 2. Deploy site to github gh-pages.
  
        $ git checkout master
        $ ./mvnw clean site-deploy
