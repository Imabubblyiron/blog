# 분할정복 알고리즘 - HeapSort


### Heap Sort의 개념


1. 완전 이진트리의 일종으로 우선순위 큐를 위하여 만들어진 자료구조이다.
2. 최댓값, 최솟값을 쉽게 추출할 수 있는 자료구조이다.
3. Max Heap이나 Min Heap tree로 구성하여 정렬하는 방법
4. Max Heap은 내림차순 정렬,, Min Heap은 오름차순 정렬로 구성할때 사용한다.
5. 분할 정복 방법중에 하나이다.


### Heap Sort의 과정


1. 아래 예시에서 중요한 것은 list의 첫 인덱스 자리에 0을 넣고 시작한다. 이유로는 인덱스가 1부터 시작해야 leftChild, rightChild의 인덱스를 쉽게 구할수 있기 때문이다.
2. 아래의 buildHeap 메서드를 보면 i가 array의 길이/2 부터 1까지 max_heapify를 실행한다. 
3. max_heapify() 실행한 후에 2*i>arr.length르 비교문을 한번 사용하는데 여기에서 하는 일은 max_heapify()의 파라미터가 루트 노드로 향하였을 때를 위한 것이다. 
    어차피 루트노드는 자식을 가지고 있지 않기때문에 연산을 할 필요가 없기 때문에 그대로 메서드를 종료시킨다.
4. leftChild는 2*i, rightChild는 leftChild+1의 규칙인 index를 가진다.
5. largest 변수를 이용하여 list[i]와 list[leftChild], list[rightChild]값을 비교하여 최대값을 largest에 저장한다 list[i]가 그 중에 제일 크면 바꿀 필요가 없기때문에 그대로 종료시킨다.
6. largest변수가 i에서 leftChild나 rightChild로 바뀐 상태면 값을 swap한 후에 list[child]값에서의 힙 정렬을 다시 시작해주어야 하기때문에 재귀를 이용한다. 
7. 위의 과정을 반복한다.


### Heap Sort의 특징


1. 시간 복잡도가 O(nlogn)으로 다른 정렬 알고리즘과 비교해 보았을때 가장 빠르편에 속한다.
2. 퀵 정렬과 다르게 최악일때도 시간복잡도가 NlogN이다.
3. 가장 큰 값이나 가장 작은 값을 찾을때 유용한 정렬이다.


아래 예시는 필자가 Java를 이용하여 퀵소트를 구현한 것이다. 중간중간의 설명을 넣어 놓았기 때문에 이해가 잘 될 것이다.


```kotlin

public class MyHeap {
 
    int[] arr;
 
    public MyHeap(int[] arr) {
        this.arr = arr;
    }
 
    public void max_Heapify(int i) {
 
        // 리프노드에서는 leftChild, rightChild가 없기 때문에 그냥 종료, 변수 할당 받기전 미리 체
        if (2*i > arr.length) 
            return;
        
        int leftChild = i * 2;
        int rightChild = leftChild + 1;
        int largest;
        
        // leftChild가 클때
        if (leftChild < arr.length && arr[leftChild] > arr[i])
            largest = leftChild;
        else
            largest = i; // 그렇지 않으면 그냥 인덱스로
        
        // rightChild가 클때
        if (rightChild < arr.length && arr[rightChild] > arr[largest])
            largest = rightChild;
 
        if (largest != i) {
            //largest index가 부모 노드보다 값이 크면 바꾸기
            swap(i, largest);
            //리프 노드에서는 상관없지만 루트노드로 향할때마다 다시 로우단까지 바꿔줘야함
            max_Heapify(largest);
        }
    }
 
    public void buildMaxHeap() {
 
        if (arr == null || arr.length < 1)
            return;
 
        for (int i = arr.length / 2; i > 0; i--) {
            max_Heapify(i);
        }
    }
 
    private void swap(int i, int j) {
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
 
    public void printArr() {
        for (int i = 0; i < arr.length; i++) {
            System.out.print(" " + arr[i]);
            if (i == 0 || i == 1 || i == 3 || i == 7) {
                System.out.println();
            }
        }
    }

}

```

개인적으로 분할정복 알고리즘 중에 HeapSort가 이해하기에 더 직관적인 것 같다.
