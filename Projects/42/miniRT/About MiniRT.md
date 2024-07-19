
# Overview

# About Subject


# Feature
necessary features of MiniRT

- Ray-Tracing Algorithm
- .rt파일 파서
- vector calculation
- 3D 오브젝트 콜라이더 -> 레이와 오브젝트의 충돌을 바탕으로 렌더링
- 3D 카메라
- 렌더링 순서 결정 알고리즘(겹침, 가려짐)
- 그림자
- 굴절
- 텍스쳐 매핑 -> 정점에 2D 이미지 좌표를 매핑
- 화면 조정

# Coding Convention
- norminette
- All resources must be allocated in the heap
- every systemcall failure goes to error message and exit(1)