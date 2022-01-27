1. The table of content in readme.md file should have pointers not figures  
2. Headings and Sub-headings 

# Heading
## Sub-heading  

3. Table 

|NAME        |INTERFACE  |BRIDGE      |  
|------------|-----------|------------|
|------------|-----------|------------|
|------------|-----------|------------|

4. Next line  
To go to next line add two spaces at the end of each line else the next sentence will come in same line.  
Next line

5. links in a word  
[OVN document](link)

6. Pointers (only till level 3)

- Pointers 1
  - Sub-Pointers 1
    - Sub-Pointers 2
    
7. Images  
![Image Name](Images/Image_Name.png)

8. If we are discussing the ways and you have differnet components then add them in pointers with bold heading rather than adding them in figures like 1,2 etc.  

- **OVN Kubenode**  
  Explanation

9. For commands you want to highlight in the sentence put it in ''.   
  It runs `oc get nodes` command.

10. Every md file has its own table of content but it is not to be written as table of content it has the heading as in the main table of content and sub-headings as pointers.
subheading should have its link.   

# Introduction

- [Overview](#overview)
- [Out of Scope](#out-of-scope)
- [Intended Audience](#intended-audience)
- [Abbreviations](#abbreviations)
- [Conventions](#conventions)

11. code to be written in below format

```
< code >

```

12. If we want to add an example there is no need for adding syntax and example seperately, instead we will only tell the example.  

Example: Create a pod 

```
$ cat egressip.yaml
apiVersion: k8s.ovn.org/v1
kind: EgressIP
metadata:
  name: egressips-prod
spec:
  egressIPs:
  - 172.25.75.201
  namespaceSelector:
    matchLabels:
      name: egressip
```
		   
13. Unifying the format in the code as if we want to write namespace name then the format for writing it in code will be < NAMESPACE_NAME >, we will add all the replacable variables such as podname namespace name any binary like this only.

14. Code will have $ sign before the command in the whole document unless command execution requires root privilege (#). 

15. For any specific code we have to depict the code with example.

16. Cluster details, personal name and credentials are not to be shared in the document.  

17. All important technical words should have first letter as capital. 
Example: Introduction of OVN CNI Plugin and SDN CNI Plugin

18. To add annotation we can directly add like this in the end of the document.

Topic[^1]
[^1]: Description   

