---
title: Git 개념
parent: Git
nav_order: 1
excerpt: Git이란 무엇이고 어떻게 사용하는가에 대한 설명입니다.
---
# Git

### 개인과 팀의 가장 큰 차이점
- 내가 알고 있는 내용(`코드`, `커밋`)이 전부가 아니다.
- 모든 내용은 위의 가정 하에 시작됩니다. 

### 개인 프로젝트에서의 Git
- 모든 `Commit` 이 무슨 내용인지 알 수 있음.
- `Conflict` 해결에 `--force` 와 `reset --hard` 이라는 치트키를 사용할 수 있음.
- `push` / `pull` 에 큰 신경 쓰지 않았음.

### 팀 프로젝트에서의 Git
- 모든 `Commit` 이 무슨 내용인지 절대로 알 수 없음.
- 따라서 `Conflict` 가 발생하는 경우, 관련된 모든 개발자가 확인이 필요함.

![area](https://private-user-images.githubusercontent.com/75231376/462915310-95321a62-b93d-4a2c-bfad-2e37a402d05c.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTE4MDc0NDAsIm5iZiI6MTc1MTgwNzE0MCwicGF0aCI6Ii83NTIzMTM3Ni80NjI5MTUzMTAtOTUzMjFhNjItYjkzZC00YTJjLWJmYWQtMmUzN2E0MDJkMDVjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MDYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzA2VDEzMDU0MFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWM4Yjg3ZjNlOWZhMzZjNjA2ZGZkNWFhZDdiZDVkZWJhYmNjYzBkYzE1MWU5ZDVmOWZhM2IyZTFmYmJkMWVhZGUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.p6Zw_egVq5fkfTz0RxuitY9oIJeGX2Mab5lil8fKII0)

### reset
- 코드가 날아감
- 옵션
    - `soft`: index 보존(add한 상태, staged 상태), 워킹 디렉터리의 파일 보존. 즉 모두 보존.
    - `mixed`: index 취소(add하기 전 상태, unstaged 상태), 워킹 디렉터리의 파일 보존 (기본 옵션)
    - `hard`: index 취소(add하기 전 상태, unstaged 상태), 워킹 디렉터리의 파일 삭제. 즉 모두 취소.
    
```bash
git reset --hard HEAD # (해당 커밋으로 돌리겠다. 모든 코드 + 커밋을)
git reset --soft HEAD # (해당 커밋으로만 돌린다.)
```
    

### Revert
- `reset` 없이 코드를 되돌릴 수 있는 명령어
- 그에 반해 revert는 앞서 말한 것 처럼 특정 사건을 골라서 없던일로 만듭니다. 또한 앞서 말한 것처럼 과거 특정 사건을 없에주지만 revert를 했다는 이력이 남게 됩니다.

```bash
git revert <commit id>
```

# Git area
![checkout](https://private-user-images.githubusercontent.com/75231376/462915369-5efbed5d-bd78-43dc-9ef0-5136ec126e97.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTE4MDcyNzksIm5iZiI6MTc1MTgwNjk3OSwicGF0aCI6Ii83NTIzMTM3Ni80NjI5MTUzNjktNWVmYmVkNWQtYmQ3OC00M2RjLTllZjAtNTEzNmVjMTI2ZTk3LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA3MDYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNzA2VDEzMDI1OVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTU3YzhmZjRjZDk4MGQ0ODZlYTlmMzdmOTk2MzQ0MzJhNTc0ZjIzMzVmYThjYmIxNDk0NGIxM2U0YTRiM2M2NTQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.J-iNJJjoSuqSZmiGzv94n3IAwW9P81gAFkysaDgtLxY)

# Commit type
- feat: 새로운 기능 추가
- fix: 버그 수정
- docs: 문서 수정
- style: 코드 포멧팅, 세미콜론 누락, 코드 변경이 없는 경우
- refactor: 코드 리펙토링
- test: 테스트코드, 리펙토링 테스트 코드 추가
- chore: 빌드 업무 수정, 패키지 매니저 수정, `.ignore` 수정 등

# upstream
- local branch 와 remote branch 를 연결

# origin

```bash
git push -u origin main //git repository에 main으로 푸쉬  --set-upstream
//관계 설정
```
