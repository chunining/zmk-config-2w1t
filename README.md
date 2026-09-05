# 2W1T Macro — ZMK 설정

## 파일 배치

GitHub 저장소 최상단에 이 구조 그대로 넣으면 됩니다.

```
build.yaml
config/
  west.yml
  macro2w1t.conf
boards/shields/macro2w1t/
  Kconfig.shield
  Kconfig.defconfig
  macro2w1t.overlay
  macro2w1t.keymap
  macro2w1t.zmk.yml
```

## GitHub 세팅

1. github.com 에서 새 저장소를 만듭니다. 이름은 아무거나, **Public** 으로.
2. 위 파일들을 업로드합니다. 웹에서 `Add file › Upload files` 로 폴더째 끌어다 놓으면 됩니다.
3. 저장소의 `Actions` 탭에 들어가 `I understand my workflows, go ahead and enable them` 을 누릅니다.
4. `.github/workflows/build.yml` 이 필요합니다. 저장소 화면에서
   `Add file › Create new file` 로 경로에 `.github/workflows/build.yml` 을 넣고
   아래 내용을 붙여넣으세요.

```yaml
on: [push, pull_request, workflow_dispatch]

jobs:
  build:
    uses: zmkfirmware/zmk/.github/workflows/build-user-config.yml@main
```

5. 커밋하면 자동으로 빌드가 돌아갑니다. `Actions` 탭에서 진행 상황을 볼 수 있습니다.
6. 초록색 체크가 뜨면 그 실행을 클릭 → 맨 아래 `Artifacts` 에서 zip 을 받습니다.
7. zip 안의 `macro2w1t-nice_nano_v2-zmk.uf2` 를 꺼냅니다.

## 굽기

1. USB 를 꽂습니다.
2. 리셋을 빠르게 두 번 누릅니다.
3. `NICENANO` 드라이브가 뜨면 `.uf2` 를 끌어다 놓습니다.
4. 드라이브가 저절로 사라지면 완료입니다.

## 검증

키맵의 `test_layer` 는 모든 위치가 서로 다른 글자를 내도록 되어 있습니다.
메모장을 열고 전부 눌러보면서 `macro2w1t.keymap` 상단의 표와 대조하세요.

- 글자가 안 나옴 → 그 자리 배선이나 납땜이 끊김
- 다른 글자가 나옴 → 행/열 배정이 어긋남
- 여러 글자가 한꺼번에 나옴 → 다이오드 방향 문제

## 참고

- 인코더 `steps` 와 `triggers-per-rotation` 은 CKW12 실측값을 모르고 넣은
  추정치입니다. 한 칸 굴렸을 때 두 번 입력되면 `triggers-per-rotation` 을
  늘리고, 두 칸 굴려야 한 번 입력되면 줄이세요.
- 인코더2 의 A 핀은 P0.31 입니다. nice!nano v2 에서는 이 핀이 배터리 전압
  측정에도 쓰입니다. 인코더 자체는 동작하지만 배터리 잔량 표시가 부정확할
  수 있습니다.
