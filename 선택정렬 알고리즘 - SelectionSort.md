# 선택정렬 알고리즘 - Selection Sort


### Selection Sort의 개념


1. 제자리 정렬 알고리즘이다. 입력 배열만을 이용하고 추가 메모리를 사용하지 않는다는 의미이다.
2. Swap할 index는 이미 알고있는 상태에서 원하는 값을 선택한 후에 swap한다.


### Selection Sort의 과정


1. 아래 예시에서 list에 정렬전 배열 입력값을 받는다.
2. 아래 예시는 오름차순이기 때문에 인덱스 0인 list 값이 제일 작은 숫자가 되어야한다. 그렇기 위해서 list[0]부터 기준으로 잡고 나머지 list[index]를 비교해나갈 것이다.
3. 2번의 과정을 통해 list[0]보다 작은 값이 있다면 그 index를 least변수에 저장해두도록 한다.  list[0]을 기준으로 한바퀴 돌고나면 list[0]보다 최솟값이 있다면 least값은 i가 아닌 상태가 된다. 그때 list[0]과 list[least]값을 swap 한다.
    어차피 루트노드는 자식을 가지고 있지 않기때문에 연산을 할 필요가 없기 때문에 그대로 메서드를 종료시킨다.
4. 위의 과정을 반복한다.


### Selection Sort의 특징


1. 자료 이동 횟수를 미리 결정한다.
2. 안정성이 없는 정렬이다. 이것은 같은 값이여도 상대적인 위치가 다를 수 있음을 의미한다.
3. Best일때 n^2, Worst일때도 n^2의 시간복잡도를 가진다.


### SelectionSort의 과정

아래 그림은 선택정렬의 과정을 나타낸다. list[index] index가 0일때부터 순차적으로 비교해서 최솟값을 찾아 바꾸는 과정이다.

![](https://mblogthumb-phinf.pstatic.net/20140128_73/justant_1390835759169oepXz_PNG/1.png?type=w2)


다음 아래 코드는 필자가 Java를 이용하여 선택정렬을 구현한 것이다. 중간중간의 설명을 넣어 놓았기 때문에 이해가 잘 될 것이다.


```java

public class MySelectionSort {

    public MySelectionSort() {
        selection_sort();
    }
    
    public void selection_sort() {
        
        int[] list = {1,2,3,0,10,3,4,5};
        
        int n = list.length;
        int least;
        int temp;
        
        for(int i=0; i<n-1 ; i++) {
            least = i;
            for(int j=i+1; j<n ; j++) {
                if(list[i] > list[j]) {
                    least = j;
                }
            }
            if(least != i) {
                temp = list[least];
                list[least] = list[i];
                list[i] = temp;
            }
        }
        
        for(int item : list) {
            System.out.println(item + " ");
        }
    }
}

```

