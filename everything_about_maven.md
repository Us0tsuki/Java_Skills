## Intro to repositories
[http://maven.apache.org/guides/introduction/introduction-to-repositories.html]

## Setting up Multiple Repositories
1. Do it in POM
```
<project>
...
  <repositories>
    <repository>
      <id>my-repo1</id>
      <name>your custom repo</name>
      <url>http://jarsm2.dyndns.dk</url>
    </repository>
    <repository>
      <id>my-repo2</id>
      <name>your custom repo</name>
      <url>http://jarsm2.dyndns.dk</url>
    </repository>
  </repositories>
...
</project>
```
2. Create a profile in settings.xml (DEFAULT: ${user.home}/.m2/settings.xml)
```
<settings>
 ...
 <profiles>
   ...
   <profile>
     <id>myprofile</id>
     <repositories>
       <repository>
         <id>my-repo2</id>
         <name>your custom repo</name>
         <url>http://jarsm2.dyndns.dk</url>
       </repository>
     </repositories>
   </profile>
   ...
 </profiles>
 
 <activeProfiles>
   <activeProfile>myprofile</activeProfile>
 </activeProfiles>
 ...
</settings>
```

 __Note:__ You must remember to activate that particular profile by registering a profile to be active in the activeProfiles element.
 
 ## Using Mirrors for Repos
 http://maven.apache.org/guides/mini/guide-mirror-settings.html