# 증적을 TXT/CSV로 변환하기

증적 로그에서 **원하는 전문만 골라 TXT 또는 CSV 파일로 저장**하는 활용입니다. 증적은 바이너리 파일이라 그대로는 검색하기 어렵습니다. 텍스트로 바꾸면 `grep`, `cat`, 편집기, 엑셀로 분석할 수 있습니다.

`kc play` 에 변환 스크립트(`script:cb_convert`)를 지정하면 됩니다.

## 기본 형식

```bash
cd $v17/bin
kc play name:rcv[이름] date:YYYYMMDD time:HHMMSS~HHMMSS \
  script:cb_convert [조건]
```

| 인자 | 설명 |
|---|---|
| `name:` | 증적 이름. 예) `rcv`, `dist`. `kc play` 의 `name:` 과 같음 |
| `date:` | 증적 날짜(`YYYYMMDD`). 생략하면 오늘 |
| `time:` | 재생할 구간 `HHMMSS~HHMMSS`. 예) `080000~110000` |
| `script:cb_convert` | 변환 스크립트 지정 |
| 조건 | 어떤 전문을 뽑을지. 아래 두 가지 방법 중 하나 |

조건 없이 실행하면 사용법을 출력하고 끝납니다.

## 방법 1 — 명령행에서 한 가지 조건으로 변환

전문의 `offset` 위치에서 `length` 길이만큼을 읽어 `pattern` 과 같은 전문만 저장합니다. 출력 파일은 `out:` 으로 지정하며, **확장자로 형식이 정해집니다.**

```bash
# TXT
kc play name:rcv date:YYYYMMDD time:080000~110000 script:cb_convert \
  offset:1 length:5 pattern:AB123 out:/tmp/result.txt

# CSV (헤더 없이 seq, hhmmss, rid, head, data 열)
kc play name:rcv date:YYYYMMDD time:080000~110000 script:cb_convert \
  offset:1 length:5 pattern:AB123 out:/tmp/result.csv
```

| 확장자 | 저장 형식 |
|---|---|
| `.txt` | 전문 한 건을 한 줄로 저장 |
| `.csv` | 콤마로 구분해 저장 |

- 출력 파일은 실행할 때마다 **덮어씁니다.** 이어서 쌓으려면 `append:true` 를 추가합니다.
- 출력 경로는 **절대경로**(`/tmp/...`)를 권장합니다. 상대경로는 `$v17/bin` 기준으로 저장됩니다.

## 방법 2 — 필터 파일로 변환

조건이 여러 개이거나 열(컬럼)을 직접 정하고 싶으면 **필터 파일**을 만들어 `with-cfg:` 로 지정합니다.

```bash
kc play name:rcv[이름] date:YYYYMMDD time:HHMMSS~HHMMSS \
  script:cb_convert with-cfg:$v17/cfg/templates/play_filters/[필터파일]
```

!!! warning "with-cfg 는 절대경로로 쓰세요"
    `kc` 는 실행할 때 작업 위치를 `$v17/bin` 으로 바꿉니다. 그래서 `with-cfg:` 에는 `$v17/cfg/...` 또는 `/home/...` 같은 **절대경로**를 써야 합니다.

자주 쓰는 필터 파일은 서버의 `cfg/templates/play_filters/` 에 있습니다. 서버에 따라 일부 파일이 없을 수 있으니 `ls $v17/cfg/templates/play_filters/` 로 확인하세요.

### 필터 파일 예 — 심볼 구조가 있는 전문

같은 전문을 세 가지 형식으로 저장하는 필터 파일이 한 벌로 제공됩니다. 파일 이름의 `[이름]` 은 대상 전문에 따라 정해집니다. 서버에 어떤 파일이 있는지는 `ls $v17/cfg/templates/play_filters/` 로 확인하세요.

| 필터 파일 | 저장 위치 | 설명 |
|---|---|---|
| `flt_[이름]_csv.moon` | `/tmp/[이름].csv` | 심볼 구조를 검색해 `seq`, `time`, `rid`, `head`, `symbol`, `data` 열로 저장합니다. 제어 바이트와 `\` 는 `\xHH` 로 바뀌어 분석과 검색에 알맞습니다. |
| `flt_[이름]_txt.moon` | `/tmp/[이름].txt` | 해당 전문 전체를 한 건당 한 줄로 저장합니다. `grep`, `cat`, 편집기로 확인할 때 씁니다. 필터 파일 안의 심볼 패턴 조건 주석을 풀면 해당 심볼의 데이터만 골라 저장할 수 있습니다. |
| `flt_[이름]_txt_raw.moon` | `/tmp/[이름]_raw.txt` | 심볼 구조만 골라 **원본 바이트를 그대로** 저장합니다. 바이트 단위 비교나 hex dump 확인에 씁니다. |

```bash
kc play name:rcv[이름] date:YYYYMMDD time:HHMMSS~HHMMSS \
  script:cb_convert with-cfg:$v17/cfg/templates/play_filters/flt_[이름]_csv.moon

head /tmp/[이름].csv
```

저장 위치는 필터 파일 안의 `out:` 으로 정해져 있습니다. 다른 곳에 저장하려면 필터 파일을 복사해 `out:` 을 바꾸고, 복사한 파일을 `with-cfg:` 로 지정하세요.

## 어떤 형식을 고를까

| 하고 싶은 것 | 고를 형식 |
|---|---|
| `grep`·`cat`·편집기로 검색하고 눈으로 확인 | **TXT (기본)** — 제어 바이트를 `\xHH` 로 바꿔 일반 텍스트 파일로 저장 |
| 엑셀·스크립트로 열(시각, 채널, 전문 종류 등)별 분석 | **CSV** — 필터 파일에 `columns` 를 지정 |
| 원본 바이트 그대로 비교, hex dump | **원본 TXT** — `escape:false` |

TXT 는 기본으로 눈에 보이지 않는 바이트(전문 종료자 `0xFF` 등)를 `\xHH` 문자열로 바꿉니다. 원본 바이트가 필요하면 필터 파일에 `escape: false` 를 넣거나 `escape:false` 를 인자로 줍니다. CSV 는 반대로 기본이 꺼져 있고, `escape:true` 를 줘야 바이트를 `\xHH` 로 바꿉니다.

!!! note "원본 바이트 파일은 텍스트 도구에서 바이너리로 보일 수 있습니다"
    `escape:false` 로 저장한 파일은 제어 바이트가 그대로 들어 있어 `grep`, `cat`, 편집기가 바이너리 파일로 인식할 수 있습니다. 검색과 확인이 목적이면 기본 TXT 를 쓰세요.
