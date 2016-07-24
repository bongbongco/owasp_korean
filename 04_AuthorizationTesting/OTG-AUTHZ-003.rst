============================================================================================
OTG-AUTHZ-003 (권한 상승 테스트)
============================================================================================

|

개요
==========================================================================================

This section describes the issue of escalating privileges from one stage to another. During this phase, the tester should verify that it is not possible for a user to modify his or her privileges or roles inside the application in ways that could allow privilege escalation attacks. 

Privilege escalation occurs when a user gets access to more resources or functionality than they are normally allowed, and such elevation or changes should have been prevented by the application. This is usually caused by a flaw in the application. The result is that the application performs actions with more privileges than those intended by the developer or system administrator. 

The degree of escalation depends on what privileges the attacker is authorized to possess, and what privileges can be obtained in a successful exploit. For example, a programming error that allows a user to gain extra privilege after successful authentication limits the degree of escalation, because the user is already authorized to hold some privilege. Likewise, a remote attacker gaining superuser privilege without any authentication presents a greater degree of escalation. Usually, people refer to vertical escalation when it is possible to access resources granted to more privileged accounts (e.g., acquiring administrative privileges for the application), and to horizontal escalation when it is possible to access resources granted to a similarly configured account (e.g., in an online banking application, accessing information related to a different user). 

|

테스트 방법
==========================================================================================

Testing for role/privilege manipulation In every portion of the application where a user can create information in the database (e.g., making a payment, adding a contact, or sending a message), can receive information (statement of account, order details, etc.), or delete information (drop users, messages, etc.), it is necessary to record that functionality. The tester should try to access such functions as another user in order to verify if it is possible to access a function that should not be permitted by the user's role/privilege (but might be permitted as another user). 

**예제**

The following HTTP POST allows the user that belongs to grp001 to access order #0001: 

.. code-block:: html

    POST /admin/addUser.jsp HTTP/1.1 
    Host: www.example.com 
    [other HTTP headers] 

    userID=fakeuser&role=3&group=grp001 

Verify if a user that does not belong to grp001 can modify the value of the parameters 'groupID' and 'orderID' to gain access to that privileged data. 

**예제**

The following server's answer shows a hidden field in the HTML returned to the user after a successful authentication. 

.. code-block:: html

    HTTP/1.1 200 OK 
    Server: Netscape-Enterprise/6.0 
    Date: Wed, 1 Apr 2006 13:51:20 GMT 
    Set-Cookie: USER=aW78ryrGrTWs4MnOd32Fs51yDqp; path=/; 
    domain=www.example.com 
    Set-Cookie: SESSION=k+KmKeHXTgDi1J5fT7Zz; path=/; 
    domain= www.example.com 
    Cache-Control: no-cache 
    Pragma: No-cache 
    Content-length: 247 
    Content-Type: text/html 
    Expires: Thu, 01 Jan 1970 00:00:00 GMT 
    Connection: close 

    <form  name="autoriz" method="POST" action = "visual.jsp"> 
    <input type="hidden" name="profile" value="SysAdmin"> 
    <body onload="document.forms.autoriz.submit()"> 
    </td> 
    </tr> 

What if the tester modifies the value of the variable "profile" to "SysAdmin"? Is it possible to become administrator? 


**예제**

In an environment where the server sends an error message contained as a value in a specific parameter in a set of answer codes, as the following: 

.. code-block:: html

    @0`1`3`3``0`UC`1`Status`OK`SEC`5`1`0`ResultSet`0`PVValid`-1`0`0` 
    Notifications`0`0`3`Command  Manager`0`0`0` StateToolsBar 
    `0`0`0` 
    StateExecToolBar`0`0`0`FlagsToolBar`0 

The server gives an implicit trust to the user. It believes that the user will answer with the above message closing the session. 
In this condition, verify that it is not possible to escalate privileges by modifying the parameter values. In this particular example, by modifying the `PVValid` value from '-1' to '0' (no error conditions), it may be possible to authenticate as administrator to the server. 

|

References 
==========================================================================================

Whitepapers 
-----------------------------------------------------------------------------------------

- Wikipedia -Privilege Escalation: http://en.wikipedia.org/wiki/ Privilege_escalation 

|

Tools 
==========================================================================================

- OWASP WebScarab: OWASP WebScarab Project 
- OWASP Zed Attack Proxy (ZAP) 

|