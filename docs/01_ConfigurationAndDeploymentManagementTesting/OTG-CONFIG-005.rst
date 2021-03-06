============================================================================================
OTG-CONFIG-005 (인프라와 어플리케이션 관리자 인터페이스 확인)
============================================================================================

|

개요
==========================================================================================

관리자 인터페이스는 특정 사용자가 사이트에서 권한 있는 활동을 수행 할 수 있도록 응용 프로그램 또는 응용 프로그램 서버에 있을 수 있습니다. 
승인되지 않은 사용자나 표준 사용자가 이 권한이 있는 기능에 액세스 할 수 있는지 여부 및 그 액세스 방법을 확인하려면 테스트를 수행해야합니다.

권한이 부여된 사용자가 사이트 작동 방식을 변경할 수 있는 기능에 액세스 할 수 있게하려면 응용 프로그램 관리자 인터페이스가 필요할 수 있습니다. 
그러한 변경에는 다음이 포함될 수 있습니다.

- 사용자 계정 프로비저닝
- 사이트 디자인 및 레이아웃
- 데이터 조작
- 구성 변경

많은 경우, 이러한 인터페이스에는 권한이없는 액세스로부터 보호 할 수있는 충분한 제어 기능이 없습니다. 
테스트는 이러한 관리자 인터페이스를 발견하고 권한이 부여 된 사용자를위한 기능에 액세스하는 것을 목표로합니다.

|

테스트 방법
==========================================================================================

|

Black Box Testing
-----------------------------------------------------------------------------------------

이번 섹션은 관리 인터페이스 존재를 테스트 하기 위해 사용되는 벡터를 설명합니다.

이 기술들은 권한 상승 관련 문제를 테스트하는데 사용될 수 있습니다.
(인증 스키마 우회 테스트(OTG-AUTHZ-002)와 안전하지 않은 직접 객체 참조 테스트(OTG-AUTHZ-004) 참고)

- 디렉토리와 파일 리스팅. Google dorks 등의 내용을 기반으로 관리 인터페이스의 경로 추측한다.
- 서버 컨텐츠를 브루트 포싱을 할 수 있는 툴이 많이 존재합니다. 테스터는 관리 페이지의 파일명도 식별해야합니다.
- 소스 코드에 있는 주석과 링크. 대부분의 사이트는 모든 사이트 사용자들에게 로드하기 위해 공통 코드를 사용합니다. 클라이언트로 전송된 모든 소스를 검사하다보면, 관리자 기능에 대한 링크가 발견될 수 있으니 검사해야합니다.
- 서버와 어플리케이션 문서 검토. 만약 어플리케이션 서버 또는 어플리케이션이 기본 설정으로 배포된 것이라면, 설정 또는 도움말 문서에 설명된 정보를 사용하여 관리 인터페이스에 저액세스할 수 있습니다. 관리 인터페이스가 발견되어 자격 증명이 요구되면, 기본 패스워드 리스트를 참고해야 합니다.
- 공개 이용 정보. 워드프레스와 같은 어플리케이션 대부분이 기본 관리인터페이스를 가지고 있습니다.
- 서버 포트 대체. 관리 인터페이스는 주요 어플리케이션과 다른 포트를 사용하기도 합니다. 예를 들어, 아파치 톰캣의 관리 인터페이스는 8080 포트를 사용합니다.
- 파라미터 침투. GET/POST 파라미터, 또는 cookie 변수를 통해 기능적으로 관리자를 활성화할 수도 있습니다.

.. code-block:: html

    <input type="hidden" name="admin" value="no">

또는, cookie에서

.. code-block:: html

    Cookie: session_cookie; useradmin=0

관리 인터페이스가 발견되면, 인증 우회를 하기 위해 위의 기술들을 조합하여 사용할 수 있습니다.
만약 실패하면, 테스터는 브루트 포스 공격을 시도해야 합니다.

그러한 경우 테스터는 관리 계정 잠금에 대한 가능성도 알고 있어야 합니다.

|

Gray Box Testing
-----------------------------------------------------------------------------------------

서버 및 응용 프로그램 구성 요소에 대한보다 자세한 조사가 강화되어야 합니다(즉, 관리자 페이지는 IP 필터링 또는 기타 제어를 통해 모든 사람이 액세스 할 수 없음). 
적용 가능한 경우 모든 구성 요소가 기본 자격 증명을 사용하지 않음을 확인하거나 구성.
소스 코드는 권한 부여 및 인증 모델이 일반 사용자와 사이트 관리자간에 명확한 직무 분리를 보장하는지 확인하기 위해 검토되어야 합니다. 
일반 사용자와 관리자 사용자가 공유하는 사용자 인터페이스 기능을 검토하여 그러한 구성 요소의 그림과 그러한 공유 기능의 정보 유출과의 명확한 구분을 보장해야합니다.

각 웹 프레임 워크에는 고유 한 관리자 기본 페이지 또는 경로가있을 수 있습니다. 예를 들어


**WebSphere:**

/admin
/admin-authz.xml
/admin.conf
/admin.passwd
/admin/*
/admin/logon.jsp
/admin/secure/logon.jsp

**PHP:**

/phpinfo
/phpmyadmin/
/phpMyAdmin/
/mysqladmin/
/MySQLadmin
/MySQLAdmin
/login.php
/logon.php
/xmlrpc.php
/dbadmin

**FrontPage:**

/admin.dll
/admin.exe
/administrators.pwd
/author.dll
/author.exe
/author.log
/authors.pwd
/cgi-bin

**WebLogic:**

/AdminCaptureRootCA
/AdminClients
/AdminConnections
/AdminEvents
/AdminJDBC
/AdminLicense
/AdminMain
/AdminProps
/AdminRealm
/AdminThreads

**WordPress:**

wp-admin/
wp-admin/about.php
wp-admin/admin-ajax.php
wp-admin/admin-db.php
wp-admin/admin-footer.php
wp-admin/admin-functions.php
wp-admin/admin-header.php


|

도구
==========================================================================================

- Dirbuster
- THC-HYDRA
- brute forcer

|

참고 문헌
==========================================================================================

- 기본 패스워드 리스트: http://www.governmentsecurity.org/articles/DefaultLoginsandPasswordsforNetworkedDevices.php
- 기본 패스워드 리스트: http://www.cirt.net/passwords

|



























