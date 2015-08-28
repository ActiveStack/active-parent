Publishing
==========
  
To deploy to maven central, follow the steps described [here](http://central.sonatype.org/pages/apache-maven.html).
A summary of these steps follows.
        
* GPG - (One time setup) Be sure you have installed and configured gpg as described 
  [here](http://central.sonatype.org/pages/working-with-pgp-signatures.html). 
  
    If gpg is not installed, you may see an error like this:

        [ERROR] Failed to execute goal org.apache.maven.plugins:maven-gpg-plugin:1.6:sign (sign-artifacts) on project active-parent: Unable to execute gpg command: Error while executing process. Cannot run program "gpg": error=2, No such file or directory -> [Help 1]        
    On Mac OSX, you can install gpg via [brew](http://brew.sh), via: `$ brew install gpg`

    If you have not configured gpg, you may see an error like this:

        [INFO] --- maven-gpg-plugin:1.6:sign (sign-artifacts) @ active-parent ---
        gpg: no default secret key: secret key not available
        gpg: signing failed: secret key not available
    Fix this error by configuring and distributing your gpg key as described [here](http://central.sonatype.org/pages/working-with-pgp-signatures.html).
   
    Setup the gpg passphrase and key name in your `.m2/settings.xml` file as described 
    [here](https://maven.apache.org/plugins/maven-gpg-plugin/usage.html) under section
    'Configure passphrase in settings.xml with a keyname':
   
          <settings>
            [...]
            <servers>
              [...]
              <server>
                <id>your.keyname</id>
                <passphrase>clear or encrypted text</passphrase>
              </server>
            </servers>
          
            [...]
            <profiles>
              <profile>
                <activation>
                  <activeByDefault>true</activeByDefault>
                </activation>
                <properties>
                  <gpg.keyname>your.keyname</gpg.keyname>
                </properties>
              </profile>
            <profiles>
          </settings>
          
    Test your gpg configuration by running: `$ ./mvnw clean verify`
   
* <a name="deploySnapshot">Deploy SNAPSHOT</a> to OSSRH for testing

    To deploy a test SNAPSHOT version to `ossrh` for testing, first ensure your artifact \<version> ends in `-SNAPSHOT`,
    then run:
  
        $ ./mvnw deploy
    To deploy the SNAPSHOT to `ossrh`, you need the following configuration in your `.m2/settings.xml` file:

        <settings>
          <servers>
            <server>
              <id>ossrh</id>
              <username>your-jira-id</username>
              <password>your-jira-pwd</password>
            </server>
          </servers>
        </settings>
    
    Once deployed, test the snapshot by consuming it from: `https://oss.sonatype.org/content/groups/public`. This
     repository is defined by the profile `ossrh-staging`. Activate the `ossrh-staging` profile 
     via: `./mvnw -Possrh-staging ...`. Make sure projects that depend on the snapshot behave as expected.
  
        <profiles>
            <!-- ActiveStack test snapshot repo -->
            <profile>
                <id>ossrh-staging</id>    	    
                <repositories>
                    <repository>
                        <id>ossrh-snapshot</id>
                        <url>https://oss.sonatype.org/content/groups/public/</url>
                        <releases>
                            <enabled>false</enabled>
                            <checksumPolicy>fail</checksumPolicy>
                        </releases>
                        <snapshots>
                            <enabled>true</enabled>
                            <checksumPolicy>fail</checksumPolicy>
                        </snapshots>                    
                    </repository>
                </repositories>
            </profile>
        </profiles>      
    
* <a name="releaseToCentral">Release artifacts to OSSRH</a> - central

    When you are ready to release a new stable version, run the following:
  
        $ ./mvnw release:prepare
        $ ./mvnw release:perform
      
    Release artifacts will be deployed into repository: `https://oss.sonatype.org/service/local/staging/deploy/maven2`

    The deployed release artifacts must be 'promoted' from the [Staging Repository](https://oss.sonatype.org/#stagingRepositories)
    by clicking the 'Close' button, and if that succeeds, the 'Release' button.
  
    * Snapshot and Release artifacts can be downloaded from: https://oss.sonatype.org/content/groups/public
    * Snapshot, Release and staged artifacts can be downloaded from staging group https://oss.sonatype.org/content/groups/staging

    After a release is complete, the preferred approach is to simply download from maven central, so there 
    is no need to use the url's above nor the ossrh-staging profile to consume released artifacts.
