# 문서 기여 가이드

KC Command 사용 문서를 수정·추가하는 방법입니다. 문서 원본은 모두 `docs/` 아래 마크다운(`.md`) 파일입니다.
저장(또는 PR 머지)하면 GitHub Actions가 자동으로 사이트를 빌드·배포합니다 — **빌드 명령을 직접 칠 필요가 없습니다.**

---

## 방법 A — GitHub 웹에서 바로 고치기 (설치 불필요)

가끔 오타·문장을 고칠 때 가장 쉽습니다.

1. GitHub에서 `docs/` 폴더의 고칠 `.md` 파일을 엽니다.
2. 오른쪽 위 **연필(✏️) 아이콘**을 클릭합니다.
3. 브라우저 에디터에서 내용을 수정합니다. (상단 **Preview** 탭으로 마크다운 미리보기 가능)
4. 아래 **Commit changes...** 버튼을 누릅니다.
   - 커밋 메시지를 적고, **"Commit directly to the `main` branch"** 또는 **"Create a new branch ... and start a pull request"** 중 선택합니다.
5. main에 커밋하면 1~2분 뒤 <https://billy-crew.github.io/kc-cmd/> 에 자동 반영됩니다.

> 💡 저장소 메인에서 키보드 `.`(마침표)를 누르면 브라우저 안에서 VS Code(`github.dev`)가 열려 여러 파일을 한 번에 편집할 수 있습니다.

---

## 방법 B — 로컬에서 편집하기 (개발자 표준)

미리보기를 보며 여러 페이지를 작업할 때 좋습니다.

```bash
git clone https://github.com/Billy-Crew/kc-cmd.git
cd kc-cmd

python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

mkdocs serve       # http://127.0.0.1:8000/kc-cmd/ 실시간 미리보기
```

`docs/*.md`를 수정하면 브라우저가 자동 새로고침됩니다. 완료하면:

```bash
git add -A
git commit -m "docs: 무엇을 바꿨는지"
git push
```

---

## 방법 C — Pull Request (여러 명이 함께 작업할 때 권장)

```bash
git checkout -b docs/작업-이름
# 수정 후
git add -A && git commit -m "docs: ..."
git push -u origin docs/작업-이름
```

GitHub에서 **Pull Request**를 생성하고 동료 리뷰를 받은 뒤 머지하면 자동 배포됩니다.

---

## 새 페이지 추가하기

1. `docs/새페이지.md` 파일을 만들고 `# 제목`으로 시작합니다.
2. `mkdocs.yml`의 `nav:` 섹션에 항목을 한 줄 추가합니다:
   ```yaml
   nav:
     - 운영:
         - 새 페이지 제목: 새페이지.md
   ```
3. 커밋/푸시하면 사이드바에 자동으로 나타납니다.

---

## 자주 쓰는 마크다운 문법

| 하고 싶은 것 | 문법 |
|--------------|------|
| 제목 | `# H1`, `## H2`, `### H3` |
| 강조 | `**굵게**`, `` `인라인 코드` `` |
| 코드 블록 | ` ```lua ` … ` ``` ` |
| 표 | `\| 열1 \| 열2 \|` + 구분선 `\|---\|---\|` |
| 콜아웃(파랑) | `!!! note "제목"` 후 4칸 들여쓰기 |
| 콜아웃(주황) | `!!! warning "제목"` |
| 다른 문서 링크 | `[텍스트](파일.md)` |
| 그림/도식 | SVG를 `<figure markdown="span">…</figure>`로 그대로 삽입 |

---

## 도식(다이어그램) 수정

PMR 문서(pmr-docs)와 동일하게 이 문서의 도식은 SVG로 되어 있어 마크다운 파일 안에 `<figure markdown="span"> ... </figure>` 형태로 들어 있습니다.
SVG 좌표를 직접 만지는 건 번거로우므로, 도식 신규 작성·수정이 필요하면 담당자(또는 Claude Code)에게 요청하는 것을 권장합니다.
