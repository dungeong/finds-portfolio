# 검증 근거와 알려진 한계

이 저장소의 주장마다 근거의 종류를 구분해 둔 문서입니다.

[← README로 돌아가기](../README.md)

## 읽기 전에

- FINDS의 서비스 코드와 이슈·PR은 **팀 GitHub Organization의 비공개 저장소**에 있습니다. 아래 파일명·메서드·이슈/PR 번호·커밋은 그 저장소 기준이며, **외부에서 열어 볼 수 있는 링크가 아닙니다.**
- 코드 조사 기준: 백엔드 `develop` 브랜치, 커밋 `918b5c2`(2026-09-22). 이 포트폴리오를 만들며 테스트를 다시 실행하지는 않았습니다.
- 테스트 건수 288·293·314·337건은 **서로 다른 시점의 전체 테스트 수**입니다. 합산하지 않으며, 어느 것도 현재 최신 건수로 쓰지 않습니다.

### 근거 표시

| 표시 | 의미 |
| --- | --- |
| **코드** | 포트폴리오 작성 중 저장소 코드·마이그레이션·API 문서·Git 이력으로 직접 확인 |
| **보고** | 당시 운영 로그, 배포 확인, 테스트 실행 결과에 대한 본인 기록 (PDF에서는 `운영` 표시) |
| **미확인** | 아직 검증되지 않았거나 마지막 확인 기준 완료 보고가 없음 |

"코드"로 표시한 테스트 항목은 **해당 테스트가 존재하고 무엇을 검증하도록 작성됐는지**를 뜻합니다. 통과 여부는 "보고"입니다.

---

## 기여 구분

Git 커밋 작성자만으로 역할을 단정하지 않았습니다. 리뷰·배포처럼 Git에 남지 않는 일은 본인 기록에 근거합니다.

| 영역 | 구현 | 리뷰·배포·확인 |
| --- | --- | --- |
| 이미지 업로드·검증, 비공개 S3 Presigned URL | 본인 | 본인 |
| 완료 기록 생성과 동시성, 기록 목록·상세·최근 조회 | 본인 | 본인 |
| 토큰 재발급 API 구간별 진단 로그 (#23) | 본인 | — |
| 로컬 PostgreSQL 개발 환경·문서 (#49) | 본인 | — |
| 미션 콘텐츠 전환 (#50) | 본인 | 본인 배포, PR 병합은 백엔드 팀원 |
| 이미지 비율 허용 오차 (#52) | 본인 | 본인 배포 |
| 게스트 후보 연결 API (#54) | 본인 | 본인 배포, PR 병합은 백엔드 팀원 |
| 회고 300자 통일 (#60) | 본인 | 본인 배포 확인, PR 병합은 백엔드 팀원 |
| 프로젝트 기반, Google 로그인, 토큰 재발급, 로그아웃 | 백엔드 팀원 | — |
| 미션 도메인, 미션 후보 발급 (#25), 가입 동의, 회원탈퇴 | 백엔드 팀원 | — |
| Google 프로필 저장·로그인 갱신 (PR #57), 공통 404 (PR #58) | 백엔드 팀원 | **본인 리뷰·배포·동작 확인** (보고) |
| 서버 장애 대응, 운영 배포 | — | 본인 (보고) |
| KPI·이벤트 정의 | PM과 공동 문서, 앱 이벤트 구현은 앱 개발자 | 본인은 기술 검토·연동 범위 조율 (보고) |

팀 구성(PM 2·디자이너 3·앱 1·백엔드 3)과 서비스명 FINDS는 본인 기록입니다. 백엔드 저장소에는 서비스명이 없습니다.

---

## 기술 스택

| 주장 | 근거 | 유형 |
| --- | --- | --- |
| Java 17 | `build.gradle` toolchain | 코드 |
| Spring Boot 4.1.0 | `build.gradle` 플러그인 | 코드 |
| Gradle 9.5.1 | `gradle-wrapper.properties` | 코드 |
| Spring Data JPA, Flyway V1–V15 | `build.gradle`, `db/migration` | 코드 |
| PostgreSQL 16 | 로컬 Compose `postgres:16`, 테스트 컨테이너 이미지 | 코드 |
| AWS SDK v2 S3, 비공개 버킷 + Presigned URL | `build.gradle`, `S3ImageStorage`, README API 절 | 코드 |
| Google idToken 검증, JWT | `GoogleTokenVerifierImpl`, `JwtTokenProvider` | 코드 |
| JUnit 5, Testcontainers | `build.gradle`, `@Testcontainers(disabledWithoutDocker = true)` | 코드 |
| 운영: AWS Lightsail, Docker | 운영 Compose는 서버의 Git 미추적 파일 | 보고 |
| 앱: Android | 팀 저장소 구성 | 보고 |

---

## 운영 장애 진단과 서버 이전 → [상세](server-incident.md)

저장소에는 이 사례의 코드·로그가 없습니다.

| 주장 | 유형 |
| --- | --- |
| 512MB 인스턴스, 응답 지연, 연결 초기화, 심한 스왑 입출력과 I/O 대기 | 보고 |
| `docker stop` 바깥 `timeout` 종료 코드 124, 앱 컨테이너 종료 코드 137 | 보고 |
| `OOMKilled` 여부 | **미확인** — OOM으로 확정하지 않음 |
| `pg_dump` 성공, `pg_restore --list` 확인 | 보고 |
| 실제 DB 복원 시험 | **미확인** |
| 스냅샷 기반 2GB 이전, JVM 메모리 조정, 고정 IP·방화벽, 재기동 | 보고 |
| 이전 후 전체 ≈1.9GiB, 사용 가능 ≈1.1GiB, swap in/out 0, ping 200 | 보고 (관찰 시점) |
| 부하 테스트, 장기 안정성 | **미확인** |

## 게스트 선택 미션의 로그인 후 후보 연결 → [상세](guest-mission-continuation.md)

| 주장 | 근거 | 유형 |
| --- | --- | --- |
| 게스트 후보는 저장하지 않음 | `MissionCandidateService.issueGuestCandidates` | 코드 |
| 완료 API가 당일 후보 아니면 `MISSION_003` | `MissionRecordService.create`, `ErrorCode.MISSION_NOT_TODAYS_CANDIDATE` | 코드 |
| 완료 API의 중복 완료·이미지 소유권 검사 유지 | `MissionRecordService.create` | 코드 |
| 실패 요청의 이미지 소유자 확인, 당일 후보 0건 | 운영 DB 조회 | 보고 |
| 연결 API 경로와 세 가지 `status` | `MissionController`, `MissionCandidateConnectionStatus` | 코드 |
| 사용자 행 `SELECT … FOR UPDATE`, 홈 발급과 같은 잠금 | `MissionCandidateService.lockUser`, `UserRepository.findByIdForUpdate`, `issueUserCandidates` | 코드 |
| 잠금 후 기준 시각 1회, KST 날짜 | `connectMissionToTodaysCandidates` | 코드 |
| 홈 발급보다 연결 API 먼저 호출, "이 API가 증명하지 않는 것" | 팀 API 문서 `mission-candidate-connection.md` | 코드 |
| 미션 행 잠금 없음 | `requireActiveMission` 주석 | 코드 |
| 동시성 테스트(PostgreSQL 16) | `MissionCandidateConcurrencyTest` | 코드 |
| 종단·정책 테스트 | `MissionCandidateConnectionIntegrationTest` | 코드 |
| 개발 시점 전체 314건, 실패·오류·스킵 0 | 당시 실행 결과. 결과 파일은 대조하지 못함 | 보고 |
| 자정 전후·잠금 대기 중 날짜 변경 테스트 | 해당 테스트 없음 | **미확인** |
| `e6cdb17` 배포·기동 확인, 기존 앱 제출 시 MISSION_003 재발생 | | 보고 |
| 연결 API 적용 앱의 종단 간 QA | 마지막 확인 기준 완료 보고 없음 | **미확인** |

## 기존 데이터를 보존한 미션 콘텐츠 전환 → [상세](mission-data-migration.md)

| 주장 | 근거 | 유형 |
| --- | --- | --- |
| 24 → 18 갱신 · 6 비활성 · 6 신규 = 30 (활성 24) | V14 마이그레이션 | 코드 |
| 삭제·ID 재사용 없음, 신규 ID 하드코딩 없음 | V14 | 코드 |
| V13 nullable key 추가, 기존 URL 보존 | V13 | 코드 |
| 활성 미션 CHECK를 URL 또는 key로 변경, `NOT EXISTS` 사용 | V14 | 코드 |
| key → Presigned URL, 없으면 기존 URL, 실패 시 예외 전파 | `MissionReferenceImageResolver.resolve` | 코드 |
| 비활성 미션 정책 | `MissionInactiveTransitionIntegrationTest` | 코드 |
| 과거 기록 상세가 현재 제목·태그를 읽음 | `MissionRecordDetailService` 주석, 팀 API 문서 | 코드 |
| 코드만 롤백 시 신규 미션 URL null | V14 신규 행은 key만 보유 + 구버전은 URL만 읽음 | 코드에서 추론 |
| V13·V14 성공, 30/24/6, 활성 이미지 key 24 | | 보고 |
| S3 403 원인(IAM 주체에 접두사 읽기 권한 없음), 접두사 한정 권한 추가 | | 보고 |
| 이미지 1건 HTTP 200, `image/jpeg` | | 보고 |
| 활성 24개 전체 이미지 다운로드 | | **미확인** |

## 회고 입력 길이 300자 통일 → [상세](review-length-validation.md)

| 주장 | 근거 | 유형 |
| --- | --- | --- |
| 기존 50/100자, 기획 300자 | 변경 커밋 diff, 본인 기록 | 코드 + 보고 |
| DTO `@Size(max = 300)` ×3 | `MissionRecordCreateRequest` | 코드 |
| 엔티티 `length = 300` ×3 | `MissionRecord` | 코드 |
| V15 `VARCHAR(300)`, 기존 마이그레이션 미수정 | V15, 변경 파일 목록 | 코드 |
| 관련 PostgreSQL 테스트 클래스 | `MissionRecordReviewColumnMigrationIntegrationTest`, `MissionRecordCompletionValidationIntegrationTest` | 코드 |
| 컨트롤러 테스트 26건 통과 | | 보고 |
| 전체 337건 중 262 성공·75 스킵 (Docker 미실행) | | 보고 |
| 관련 PostgreSQL 테스트 11건 성공 | | 보고 |
| `918b5c2` 배포, ping 200, V15 success, 세 컬럼 길이 300 | | 보고 |
| 실제 앱 300자 제출 | | **미확인** |
| 사용자 보고 실패 요청의 로그 추적 | | **미확인** |

## 그 밖의 기여

| 주장 | 근거 | 유형 |
| --- | --- | --- |
| #52 3:4 정확 일치 → 상대 오차 0.5% 이하 허용 | `ImageValidator`(허용 오차 1/200), README | 코드 |
| #52 회전·크롭·리사이즈 미추가, 경계값 테스트, 거부 시 크기 로그 | `ImageValidator`, `ImageValidatorTest` | 코드 |
| #52 당시 전체 293건 통과, 배포 | | 보고 |
| #52 실제 실패 사진 해결 여부 | | **미확인** |
| #49 PostgreSQL 16 로컬 Compose, 환경변수·실행 문서 | `compose.local.yml`, README | 코드 |
| 토큰 재발급 API에만 traceId 진단 로그 | `AuthController`, `TokenRefreshService` | 코드 |

## KPI 협업 → [상세](analytics-collaboration.md)

| 주장 | 유형 |
| --- | --- |
| 발견한 문제, 제안 기준, PM과 정리된 방향 | 보고 (팀 문서·대화, 비공개) |
| 서버 오늘 후보의 KST 기준 | 코드 (`TimeConfig`, `KOREA_ZONE`) |
| 분석 선택 동의 API | 조사 기준 커밋에 없음 (코드). #62 진행 상태는 보고 |
| #62 구현·테스트·배포 | **미확인** |
| BigQuery 연결·적재, 지표 성과 | **미확인 / 없음** |

## 공개 범위

공개 자료에서 다음을 제외하거나 일반화했습니다.

- AWS 계정 ID, 운영 IP, IAM 주체 이름, 버킷 이름, S3 접두사 실제 값, Presigned URL
- 사용자 식별값, 이미지 식별값, 토큰, 환경변수 값
- 팀원 실명과 계정, 팀 대화·문서 원문과 캡처
- 서비스 소스 코드 (설명에 필요한 부분은 직접 작성한 의사코드와 구조도로 대체)
