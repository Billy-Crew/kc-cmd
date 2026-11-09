# KC Command Usage

**KC Command** 사용 문서입니다. [pmr-docs](https://github.com/Billy-Crew/pmr-docs)와 동일한 구성(MkDocs Material)이며, 내용은 `docs/` 아래 마크다운으로 작성합니다.

- 🌐 배포 사이트: <https://billy-crew.github.io/kc-cmd/>
- 📝 문서 원본: `docs/*.md`
- 🎨 커스텀 스타일: `docs/stylesheets/extra.css` (pmr-docs와 동일)

## 배포 (자동)

`main` 브랜치에 push하면 GitHub Actions(`.github/workflows/deploy.yml`)가
자동으로 MkDocs를 빌드해 GitHub Pages에 배포합니다. **글 쓰는 사람은 빌드를 신경 쓸 필요가 없습니다.**

## 로컬 미리보기

```bash
python3 -m venv .venv && source .venv/bin/activate   # 최초 1회
pip install -r requirements.txt
mkdocs serve
# 브라우저에서 http://127.0.0.1:8000/kc-cmd/ 접속
```

## 문서 수정 방법

자세한 내용은 [CONTRIBUTING.md](CONTRIBUTING.md)를 참고하세요. 요약:

- **가끔 고칠 때** — GitHub 웹에서 `docs/`의 `.md` 파일 → 연필(✏️) 아이콘 → 수정 → Commit
- **자주 쓰는 개발자** — 로컬 clone + `mkdocs serve` 미리보기 + PR
- **새 페이지 추가** — `docs/새파일.md` 생성 후 `mkdocs.yml`의 `nav:`에 한 줄 추가

## 구조

```
kc-cmd/
├─ docs/                     # 문서 마크다운 (여기를 수정)
│  ├─ index.md               # 첫 페이지
│  ├─ assets/                # 로고·파비콘
│  └─ stylesheets/extra.css  # 폰트·색 커스터마이즈
├─ overrides/.icons/custom/  # 다크/라이트 토글 아이콘
├─ mkdocs.yml                # 사이트 설정 + 네비게이션(nav)
├─ requirements.txt          # mkdocs-material
└─ .github/workflows/deploy.yml   # 자동 배포
```
