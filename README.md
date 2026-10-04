# 구매·조달 AI 데일리 브리핑

구매/조달 업무에 활용할 수 있는 최신 AI 뉴스를 수집하고, OpenAI API로 한국어 실무 브리핑을
작성해 매일 **오전 9시(한국 시간)** 이메일로 보냅니다. GitHub Actions 예약 실행은 다소 지연될
수 있으며, 중요한 업무라면 별도 스케줄러 사용을 권장합니다.

## 설정

1. 이 저장소를 GitHub에 올린 뒤 **Settings → Secrets and variables → Actions**에서 다음
   Repository secrets를 등록합니다.

   | 이름 | 설명 |
   |---|---|
   | `OPENAI_API_KEY` | OpenAI API 키 |
   | `BRIEFING_RECIPIENT` | 받을 이메일 주소 |
   | `SMTP_HOST` | SMTP 서버(예: Gmail은 `smtp.gmail.com`) |
   | `SMTP_USERNAME` | SMTP 로그인 사용자명 |
   | `SMTP_PASSWORD` | SMTP 비밀번호 또는 앱 비밀번호 |
   | `SMTP_PORT` | 선택 사항. SSL 기본값은 `465` |
   | `BRIEFING_SENDER` | 선택 사항. 발신 주소(기본값: SMTP 사용자명) |

2. 필요하면 Repository variables에 `SMTP_SECURITY`(`ssl` 또는 `starttls`)와
   `OPENAI_MODEL`을 추가합니다.
3. **Actions → Daily procurement AI briefing → Run workflow**에서 한 번 수동 실행해
   메일 수신을 확인합니다. 이후 워크플로가 매일 `00:00 UTC`, 즉 `09:00 Asia/Seoul`에 예약됩니다.

예약 워크플로는 GitHub의 **기본 브랜치에 이 파일이 병합된 뒤에만** 자동 실행됩니다. PR 브랜치에만
있는 동안에는 예약 실행되지 않으므로, PR을 병합한 다음 Actions 탭에서 수동 실행으로 먼저
검증하세요. 필수 Secret이 빠졌다면 워크플로가 누락된 이름을 오류 메시지로 표시하고 메일 생성 전에
종료합니다.

> Gmail을 사용하는 경우 일반 계정 비밀번호가 아니라 2단계 인증 후 발급한 앱 비밀번호를
> `SMTP_PASSWORD`에 넣으세요. 비밀 값은 코드나 로그에 입력하지 마세요.

## 로컬 실행

Python 3.11 이상에서 다음 환경 변수를 설정한 후 실행합니다.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export OPENAI_API_KEY="..."
export BRIEFING_RECIPIENT="me@example.com"
export SMTP_HOST="smtp.example.com"
export SMTP_USERNAME="..."
export SMTP_PASSWORD="..."
python briefing.py --dry-run  # 생성만 하고 메일은 보내지 않음
python briefing.py            # 실제 발송
```

`LOOKBACK_HOURS`(기본 36)와 `MAX_ARTICLES`(기본 15) 환경 변수로 수집 범위를 조절할 수 있습니다.
뉴스 수집은 Google News RSS를 사용하므로 실행 환경에서 외부 네트워크 접속이 필요합니다.

## 테스트

```bash
python -m unittest discover -s tests -v
```
