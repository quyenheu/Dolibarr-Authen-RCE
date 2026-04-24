# Authentication Path Traversal To Remote Execute Code

**1. INFORMATION**

- During my research, I discovered a path traversal vulnerability in Dolibarr that leads to the attacker being able to read all files on the victim system.The cause of the vulnerability is because the source code does not filter the input from the parameter menu and pass it directly to the dol_include_once() function.

**2. SOURCE ANALYST**

- Version used: Dolibarr 9.0.1
- Line 188 in file htdocs/core/get_menudiv.php, variable $file_menu is receiving input from parameter menu
![image](https://hackmd.io/_uploads/H1EjMQpEke.png)
- Next, the $file_menu variable is passed to the dol_include_once() function with the parameter \$dirmenu."standard/".\$file_menu. And the $file_menu variable is concatenating the string at the end, so we have complete control over this parameter.
![image](https://hackmd.io/_uploads/rJJQXQaEyx.png)
- The dol_include_once function is responsible for qualifying the input and performing include or include_once $fullpath variable
![image](https://hackmd.io/_uploads/SJe14Qa41l.png)
- The variable $fullpath created from $relpath is not filtered through payload path traversal
***-> From here, the attacker can include any file on the system***
- Next, you need an account that can upload documents
![image](https://hackmd.io/_uploads/HkCTFQpNkl.png)
![image](https://hackmd.io/_uploads/HkC-9QT4ye.png)
- Because the include() function will retrieve the content of the file and if it is PHP code, it will execute the code in it, so from here the attacker can completely remotely execute the code on the victim server.
![image](https://hackmd.io/_uploads/SyqBYQTNJe.png)

**3. ATTACK**

- Require: What the attacker needs is any user account, even with low authority
- This is the extremely low permission account that I will use to perform the attack
![image](https://hackmd.io/_uploads/rysmvm64ye.png)
- Access the path core/get_menudiv.php with the menu parameter being payload path traversal
![image](https://hackmd.io/_uploads/HJLdBQT4Jl.png)
![image](https://hackmd.io/_uploads/SkbCBm641g.png)
- With an account that can upload documents, the attacker can RCE the victim's server by including the documents file or any file with PHP code controlled by the attacker and posting it to the website.
![image](https://hackmd.io/_uploads/SyqBYQTNJe.png)

**4. FIX**
- Filtering the payload by adding the regex GETPOST('menu', 'aZ09') in later versions has prevented the attacker from attacking. But versions without regex will be exploited successfully
