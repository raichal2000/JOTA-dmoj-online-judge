# Setting JCloud Instance

*Last Update: 9. Jul. 2021*

**JCloud 가이드 레퍼런스**: https://jcloud-devops.github.io/user-guide.html

1. jcloud.jbnu.ac.kr 에 접속하여 로그인합니다.

2. Key Pairs 메뉴로 이동해서 Key Pair를 생성합니다. 이미 가지고 있는 경우 생략합니다.

3. Instances 메뉴로 이동합니다.

4. Launch Instance 버튼을 누릅니다.

5. 아래 명세를 만족하는 인스턴스 spec을 작성합니다.

    ```
    [Details]
      Instance Name = <your-instance-name>
      Description = <your-description>

    [Source]
      Select Boot Source = "Instance Snapshot"
      Create New Volume = "No"
      Available => Allocated = ["dmoj_v2.1.1"]

    [Flavor]
      Available => Allocated = ["cse-services.flavor"]

    [Networks]
      Available => Allocated = ["cse-students.network"]

    [Network Ports]   -- SKIP --
    [Security Groups] -- SKIP --

    [Key Pair]
      Available => Allocated = [<your-ssh-key>]

    [Configuration]   -- SKIP --
    [Server Groups]   -- SKIP --
    [Metadata]        -- SKIP --
    ```

6. Launch Instance 버튼을 누릅니다.

7. 페이지를 새로고침하여 인스턴스가 생성됐는지 확인합니다.
    ```
     Instance Name       | Image Name     | IP Address 
    --------------------------------------------------------------------
     <your-instance-name>  dmoj_v2.1.1      10.0.ddd
    --------------------------------------------------------------------
     Flavor              | Key Pair       | Status     | Availability | ...
    --------------------------------------------------------------------
     cse-services.flavor   <your-ssh-key>   Active       nova
    ```
8. 생성한 인스턴스로 SSH 접속합니다.
    ```
    IP = 왼쪽 상단 JCloud 로고 [cse-students(xxx.xxx.xxx.xxx) ▼] 중 "xxx.xxx.xxx.xxx" 입력
    Port = 생성한 Instance의 사설 IP [10.0.0.ddd] 에서 "19ddd" 입력
    ```

9. 유저 `ubuntu`의 비밀번호를 재설정합니다.
    ```
    $ sudo passwd ubuntu
    New password: 
    Retype new password: 
    passwd: password updated successfully
    ```

10. git 기록을 남길 본인의 이름과 이메일을 설정합니다.
    ```
    $ git config --global user.name "Gildong Hong"
    $ git config --global user.email "gildonghong@example.com"
    ```
