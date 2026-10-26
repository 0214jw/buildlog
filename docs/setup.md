# BuildLog 실행 환경 준비 안내

## 1. 현재 상태

2026-10-26 Phase 0에서 환경만 확인했습니다. 서비스 코드·가상환경·의존성 파일·DB·환경 설정 파일은 아직 만들지 않았습니다. 아래 실행 명령은 구현 후 사용할 예정이며 지금 실행 가능한 서비스가 있다는 뜻은 아닙니다.

## 2. 확인된 로컬 도구

| 도구 | 확인 결과 |
| --- | --- |
| Python | 3.13.14 |
| Python 실행 경로 | `C:/Users/aicam/AppData/Local/Programs/Python/Python313/python.exe` |
| pip | 26.1.2 |
| Node.js | 24.22.1 |
| npm | `npm.cmd --version`으로 11.19.1 확인 |
| Python 내장 SQLite3 | SQLite 3.50.4, 메모리 DB 연결·조회 성공 |
| FastAPI | 현재 Python 환경에 설치되지 않음. Phase 1에서 프로젝트 가상환경에 설치 예정 |

PowerShell 실행 정책이 `Restricted`여서 `npm.ps1`은 실행되지 않습니다. `npm.cmd`는 정상 동작합니다. 이후 Windows 명령은 `npm.cmd`를 사용하고 전역 실행 정책은 변경하지 않습니다. Python 가상환경도 활성화 스크립트 대신 실행 파일 경로로 사용할 수 있습니다.

## 3. 선택한 기술 기본안

프론트는 React + Vite + JavaScript를 사용합니다. 백엔드는 FastAPI + Uvicorn을 사용합니다. DB 접근은 SQLAlchemy, DB 구조 변경 관리는 Alembic, Supabase PostgreSQL 드라이버는 Psycopg를 사용합니다. 이 도구 선택은 제품 기능을 추가하지 않습니다.

| 항목 | 확인한 배포 버전 | 적용 시점 |
| --- | --- | --- |
| FastAPI | 0.162.0 | Phase 1 |
| Uvicorn | 0.54.0 | Phase 1 |
| Pydantic Settings | 2.16.1 | Phase 1 환경 설정 |
| React / React DOM | 19.3.1 / 19.3.1 | Phase 1 |
| Vite | 8.3.5 | Phase 1 |
| Vite React 플러그인 | 6.1.2 | Phase 1. 실제 템플릿 의존성도 확인 |
| SQLAlchemy | 2.1.6 | Phase 2 |
| Alembic | 1.20.1 | Phase 2 |
| Psycopg | 3.3.6 | Supabase 연결 준비·확인 시 |
| pytest / HTTPX | 9.1.1 / 0.28.1 | API 자동 테스트 단계 |

버전은 확인일의 PyPI·npm 공식 배포 메타데이터를 조회한 기준입니다. Python 최소 버전과 Vite의 Node 요구 범위는 현재 설치 버전과 맞습니다. 주요 Python 확장 패키지의 Python 3.13 Windows 64비트 배포 파일도 확인했습니다. 실제 의존성 전체 설치·테스트는 아직 수행하지 않았으므로 검증 완료 조합이라고 표현하지 않습니다. 설치 단계에서 의존성 해결과 실행을 확인한 뒤 정확한 버전과 잠금 파일을 기록합니다.

SQLAlchemy는 SQLite와 PostgreSQL을 지원하며 Alembic은 SQLAlchemy 기반 DB 구조 변경 도구입니다. SQLite와 Supabase의 제약·날짜·검색 동작이 모두 같다고 가정하지 않고 Phase 13에서 따로 확인합니다. 관련 근거는 아래 공식 문서를 참고합니다.

- [React 빌드 도구 안내](https://react.dev/learn/build-a-react-app-from-scratch)
- [Vite 실행 환경 요구 사항](https://vite.dev/guide/)
- [SQLAlchemy 지원 DB](https://www.sqlalchemy.org/features.html)
- [Alembic 안내](https://alembic.sqlalchemy.org/en/latest/)
- [Supabase DB 연결 안내](https://supabase.com/docs/guides/database/connecting-to-postgres)
- [FastAPI 배포 메타데이터](https://pypi.org/pypi/fastapi/json)
- [React 배포 메타데이터](https://registry.npmjs.org/react/latest)
- [Vite 배포 메타데이터](https://registry.npmjs.org/vite/latest)

## 4. 주소와 데이터 위치

| 용도 | 기본안 |
| --- | --- |
| FastAPI | `http://127.0.0.1:8000` |
| React | `http://localhost:5173` |
| 화면에서 접근할 API 주소 | `http://127.0.0.1:8000` |
| API 요청을 허용할 로컬 화면 출처 | `http://localhost:5173` |
| 로컬 개발 DB | 프로젝트 루트 기준 `database/buildlog.sqlite3` |
| 자동 테스트 DB | 테스트별 메모리 또는 임시 SQLite3 DB. 개발 DB와 분리 |
| 실제 서비스 DB | Supabase PostgreSQL. 실제 연결 정보는 이후 로컬 설정 |

점검 시 포트 8000·5173의 수신 프로세스는 없었습니다. 서버를 시작하지 않았으므로 실제 바인딩 성공은 Phase 1에서 확인합니다. 로컬 DB 경로는 실행 폴더에 따라 달라지지 않도록 앱 설정에서 절대 경로로 해석합니다.

Supabase는 우선 FastAPI 서버에서 SQLAlchemy/Psycopg로 PostgreSQL에 연결하는 기본안을 사용합니다. 직접 연결과 세션 풀러 중 어느 것을 쓸지는 실제 프로젝트와 네트워크를 확인해 정합니다. SQLite3에서 먼저 개발할 때는 연결 정보가 필요하지 않습니다. DB의 외부 노출 경로와 접근 권한은 Supabase 연결 단계에서 함께 확인합니다.

## 5. 예정 환경 변수

아래는 항목 설계입니다. `.env`나 `.env.example` 파일을 만든 것은 아닙니다.

| 파일 위치 기본안 | 항목 | 용도·값의 성격 |
| --- | --- | --- |
| `backend/.env` | `APP_ENV` | 로컬·테스트·실제 환경 구분 |
| `backend/.env` | `DATABASE_URL` | 로컬은 SQLite URL, 실제는 Supabase PostgreSQL 연결 문자열. 실제 연결 비밀번호 포함 시 비밀값 |
| `backend/.env` | `CORS_ORIGINS` | 허용할 React 주소 목록 |
| `frontend/.env` | `VITE_API_BASE_URL` | 브라우저에서 보이는 API 주소. 비밀값 아님 |

인증 관련 항목은 인증 방식 결정 후 추가 설계합니다. Supabase Auth용 항목이나 자체 로그인용 서명 비밀값을 임의로 확정하지 않습니다. PostgreSQL 연결 방식만 사용한다면 Supabase API 키를 무조건 요구하지 않습니다. 서버 전용 비밀값을 `VITE_` 항목에 넣지 않습니다. 각 폴더의 `.env.example`에는 항목 이름과 비밀값 없는 설명만 작성합니다.

## 6. 구현 후 사용할 예정 명령

Phase 1 구현 후 프로젝트 루트에서 실행할 서버 명령 기본안:

```powershell
.\backend\.venv\Scripts\python.exe -m uvicorn app.main:app --app-dir backend --host 127.0.0.1 --port 8000 --reload
```

React 명령 기본안:

```powershell
npm.cmd --prefix frontend run dev -- --host localhost --port 5173 --strictPort
```

자동 테스트를 만든 뒤 사용할 명령 기본안:

```powershell
.\backend\.venv\Scripts\python.exe -m pytest backend/tests
npm.cmd --prefix frontend run build
```

프론트 빌드는 기능 테스트를 대신하지 않습니다. React 자동 테스트 도구·명령과 DB 구조 적용 명령은 해당 설정을 만들 때 이 문서에 추가합니다. 지금은 설치·서버 실행·DB 적용 명령을 수행하지 않습니다.
