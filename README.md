# ZMK Studio 붙이기 — 바꿀 파일 3개

## 먼저: 지금 잘 되는 펌웨어를 백업하세요

방금 받은 `.uf2` 파일을 다른 폴더에 복사해 두세요.
Studio 설정이 안 맞으면 그걸 다시 구워서 원래대로 돌아갈 수 있습니다.

## 바꿀 파일

| 저장소 경로 | 바뀌는 것 |
|---|---|
| `build.yaml` | Studio 켜는 옵션 추가 |
| `config/boards/shields/macro2w1t/macro2w1t.overlay` | 물리 레이아웃 추가, chosen 수정 |
| `config/boards/shields/macro2w1t/macro2w1t.keymap` | 잠금 해제 키 추가 |

`macro2w1t.conf`는 그대로 둡니다. 어제 넣은 `CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y` 가
거기 있어야 휠 회전이 계속 동작합니다.

GitHub 웹에서 각 파일을 열고 → 연필(✏️) → 내용 전부 지우고 → 이 폴더의 같은 이름 파일
내용을 붙여넣기 → Commit 하시면 됩니다.

## 무엇이 달라졌나

**overlay**
- `#include <physical_layouts.dtsi>` 추가
- `chosen` 에서 `zmk,matrix-transform` 줄 삭제 — Studio는 물리 레이아웃이
  transform 을 갖고 있어야 동작하고, 둘 다 있으면 시작하지 않습니다
- `physical_layout0` 노드 추가 — 키 25개가 실제로 어디 붙어 있는지 좌표로 기술.
  전부 `2W1TMacro.kicad_pcb` 에서 뽑은 값입니다 (1u = 19.05mm 그리드 정확히 일치)

**keymap**
- 레이어 2개로 분리
- 우하단 키(원래 `R`)가 `&mo 1` — 누르고 있는 동안 Studio 레이어
- Studio 레이어의 좌상단이 `&studio_unlock`

즉 **우하단 키를 누른 채로 좌상단 키를 누르면** 잠금이 풀립니다.
실수로 눌릴 일이 없고, `R` 하나만 잃습니다.

**build.yaml**
- `snippet: studio-rpc-usb-uart` — USB 로 Studio 와 통신하는 통로
- `cmake-args: -DCONFIG_ZMK_STUDIO=y` — Studio 기능 켜기

## 굽고 나서

1. 새 `.uf2` 를 평소대로 굽습니다
2. USB 로 연결한 채 브라우저에서 **https://zmk.studio** 접속
   (Chrome, Edge 등 WebSerial 되는 브라우저여야 합니다. Firefox 는 안 됩니다)
3. `Connect` → 포트 목록에서 키보드 선택
4. 잠겨 있다고 나오면 **우하단 키 누른 채 좌상단 키** 를 누릅니다
5. 키맵 화면이 뜨면 성공

데스크톱 앱도 있습니다. 브라우저가 안 되면 https://zmk.studio 에서 받으세요.

## 주의: 이때부터 키맵 파일은 무시됩니다

Studio 로 키맵을 한 번이라도 저장하면, 그 뒤로는 **보드에 저장된 설정이 우선**입니다.
GitHub 의 `.keymap` 파일을 고쳐서 다시 구워도 반영되지 않습니다.

파일 쪽으로 되돌리려면 Studio 에서 `Restore Stock Settings` 를 실행하세요.

## 안 될 때

- **Studio 가 키보드를 못 찾음** → 케이블이 데이터용인지, 다른 USB 포트, 브라우저 확인
- **연결은 되는데 잠금이 안 풀림** → 우하단 키를 확실히 누른 상태에서 좌상단을 누르세요
- **빌드 실패** → Actions 로그의 `error:` 부분 확인. 백업해 둔 `.uf2` 로 언제든 복귀 가능
