## Troubleshooting Cloudbreak CLI

### Special characters in blueprint name cause an error  

When registering a blueprint via `blueprint create` CLI command, if the name of the blueprint includes one or more of the following special characters `@#$%|:&*;` you will get an error similar to:  

<pre>cb blueprint create from-url --name test@# --url https://myurl.com/myblueprint.bp  
[1] 7547
-bash: application.yml: command not found
-bash: --url: command not found
 ~  integration-test  1  time="2018-02-01T12:56:44+01:00" level="error" msg="the following parameters are missing: url\n"</pre> 
 
*Solution:*  
When using special characters in a blueprint name, make sure to use quotes; for example "test@#":  

<pre>cb blueprint create from-url --name "test@#" --url https://myurl.com/myblueprint.bp</pre>
 
____________________________


