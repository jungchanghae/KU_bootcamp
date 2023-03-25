# Debugger in VSCODE

## Debugger를 하는 이유
- 코드 오류가 나는 부분을 찾기 용이하다.
- 코드 중단점을 통해 중단한 후 `DEBUG CONSOLE`을 통해 값을 변경하면서 실행해볼 수 있다.
- 한 행씩 진행해보면서 값이 적절히 변하는지 확인할 수 있다.

## 사용예시 (ML)
- 실험할 Data Loader를 변경할 때 Data의 형식이 안 맞는 경우 변화를 확인한다.
- Train Code에서 optimizer, loss들이 잘 작동하는지 확인한다.
- 중단점을 이용해 OOM이 생길 경우 batchsize와 같은 parameter를 수정하여 작동할 수 있는지 확인한다. (주의 : 원래 있던 값과 다른 type도 입력되므로 항상 확인해야함.)
