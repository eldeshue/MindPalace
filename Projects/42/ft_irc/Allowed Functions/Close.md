---
Date: 2024-11-19
---
# Overview

파일 FD를 닫고, 해당 FD가 참조하고 있는 파일의 참조 횟수를 1 감소시킨다.

일반 파일 및 소켓과 같은 special한 파일도 모두 제어 가능하다.

# Signature


``` C
 #include <unistd.h>

int close(int** _fd_**);
```

## Parameters

제거하고자 하는 파일의 fd.

## Return values

성공하면 0, 실패하면 -1을 반환한다.
