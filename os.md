- 심볼릭 링크와 하드 링크의 차이
    - 링크: 서로 다른 디렉토리에서 같은 파일에 접근하는 방
    - 하드링크: 원본파일과 동일한 inode를 공유
        - 원본 파일이 삭제되어도, 하드링크를 통해 원본 파일에 접근할 수 있음
    - 심볼릭 링크: 다른 inode를 참조하지만, datablock은 원본 파일 경로 넣음
        - 원본 파일이 삭제되어도, 심볼릭 링크에게 알려지지 않음.
- 리눅스 파일 시스템 ★
    - 리눅스는 ext4(Extended File System)를 사용하고 있음
        - 윈도우는 NTFS, 맥은 APFS
    - 파일의 할당: 새로 생성할 파일을 디스크에 할당하는 방법
        - 블록: 하드 디스크와 데이터를 주고받을 때 상요되는 논리적인 단위 (일반적으로 4KB)
        - 파일을 메타데이터와 데이터로 나눠져 있음
            - 메타데이터: 파일에 대한 정보를 담는 데이터(파일 길이, 마지막 수정 시간, 접근 권한)
                - inode 자료구조 형태로 저장되고, inode 블록에 저장
            - 데이터: 실제로 가지고 있는 데이터
                - 데이터를 실제 저장하는 블록을 데이터블록이라고 함
        - EXT에선 디스크를 여러개의 블록 그룹으로 나누고, 하나의 블록 그룹안에 inode table, data blocks를 포함한 여러 블록으로 나뉘어짐.

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c5408b4-ab2e-4753-963a-024da0934c8b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c5408b4-ab2e-4753-963a-024da0934c8b/Untitled.png)

            블록 구조

            - inode table: data blocks을 참조하는 inode 블록을 담고 있습니다.
            - Data blocks: 실제 데이터가 담겨있음
            - Data blocks bitmap, Inode Bitmap: Data block, Inode 사용여부 정보를 담고있음

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f733168d-a7a2-4abc-92b6-e0afe93a2a55/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f733168d-a7a2-4abc-92b6-e0afe93a2a55/Untitled.png)

            inode 테이블

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/11e4532a-3ff1-4871-b1e2-01be94a86368/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/11e4532a-3ff1-4871-b1e2-01be94a86368/Untitled.png)

            inode 블록에서 데이터 블록을 참조하고 있는 구조

        - inode는 크게 Direct blocks와 Indirect blocks로 나눠집니다.
        - DIrect blocks는 데이터 블록을 직접 참조합니다.
        - Indirect Blocks는 데이터 블록을 참조하는 블럭을 참조합니다. (single, double, triple은 1단계, 2단계, 3단계 나뉘어서 저장합니다)
        - 이런 복잡한 구조를 갖는 이유는?
            - 더 많은 데이터를 참조하기 위해서
            - Direct blocks만 사용했을 때: 12 * 4KB = 48KB (속도는 빠르나, 저장용량이 적음)
            - Direct blocks + Indirect blocks를 사용했을 때: (12 + 1K + 1M + 1G) * 4KB = 4TB
    - 파일의 접근: 사용자가 원하는 파일을 디스크에서 접근하는 방법
        - 리눅스 파일 시스템에서는 디렉토리도 파일처럼 취급
            - 파일과 마찬가지로 inode  blocks, Data block이 존재

            ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a6372342-9a12-449c-ba02-aed4ffc862d7/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a6372342-9a12-449c-ba02-aed4ffc862d7/Untitled.png)

            - Data blocks에는 디렉토리 안에 있는 파일의 이름과 inode가 테이블 형태로 존재
            - 디렉토리 데이터 블록에 디렉토리와 관련된 INODE가 있을 수 있음
                - 그래서 계층적으로 디렉토리 안에 디렉토리를 참조하여 파일을 찾을 수 있음
                - /main/class/SpringApplicatino.class
    - 파일의 보호: 파일의 접근 권한을 관리하는 방법
        - Access Control List (ACL): 누가 어떤 연산을 할 수 있는지 리스트 형식으로 관리

            ```
            user:junroot:rwx
            group:wooteco:r-x
            ```

        - 접근 권한 비트: 파일의 소유자, 공유하는 그룹, 기타 사용자들의 접근 권한을 고정적인 9비트로 나타낸다.

            ```
            rwx(소유자)r--(공유그룹)r--(기타사용자)
            ```

        - **디렉토리**에서의 read, write, execute
            - read: 디렉토리 내의 파일의 리스트 접근 권한
            - write: 디렉터리 내 파일의 생성, 삭제, 이름 변경 권한
            - execute: 디렉토리 접속(cd 명령어) 또는 디렉토리 내의 파일 접근 권한
    - 파일의 일관성: 파일의 내용이 손상되지 않도록 해주는 방법
        - 파일을 생성하다가 디스크 전원이 꺼지게 되어도 파일 시스템을 문제없이 사용할 수 있도록 보장
        - fsck(File System Checker): 파일에 불일치되는 정보가 있는지 확인해주는 검사기
            - 문제점
                - 모든 디렉토리 구조를 스캔해야되기 때문에 상당히 느림
                - 파일의 일관성이 깨져잇는 것을 탐지해도 복구가 불가능한 경우가 많음
        - 저널링
            - inode나 비트맵의 수정이 있으면 그 내용을 **로그**로 남긴다.
            - 디스크에 변경사항이 기록되면 저널영역에 기록을 함 (하나의 파일 연산에 기록되는 로그의 집합)
            - 기록이 완료되면 inode와 비트맵에도 반영합니다.
            - 장점
                - 로그 작성중에 크래시가 발생하는 경우
                    - 실제 파일에 반영된 것이 아니기 때문에 일관성이 깨지지 않음
                - 실제 파일에 반영하는 중에 크래시가 발생하는 경우
                    - 저널에 기록된 로그를 기반으로 다시 반영하면 된다.
- 리눅스 메모리 관리
    - [https://youtu.be/qxmdX449z1U](https://youtu.be/qxmdX449z1U)
    - 메모리가 고갈되면 어떤 현상이 발생하나요?
        - 프로세스들의 Swap이 활발해지면서 CPU 사용률 하락 → CPU IDLE 상태가 됨.
        - CPU 사용률이 하락된 걸 본 운영체제는 프로세스를 추가. Thrasing 발생
        - 쓰레싱이 해소되지 않을 경우 OutOfMemory 상태로 판단
        - 중요도가 낮은 프로세스를 찾아 강제 종료
    - CPU 사용률을 계속 체크해야하는 이유
        - 특정 시점만 체크한 경우 CPU 사용률이 높아보일 수 있음
        - 연속 체크시 CPU 사용률이 급격하게 떨어지는 구간 발견 가능성
        - 메모리 적재량을 함께 체크하면서 쓰레싱 유무 확인
        - 추가적인 서버자원을 배치하는 등 해결방안 마련.

이대 반효경 교수님 KOCW 정리: [https://jhi93.github.io/os/](https://jhi93.github.io/os/)