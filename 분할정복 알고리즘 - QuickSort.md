# 분할정복 알고리즘 - QuickSort



### Quick Sort의 개념


1. '찰스 앤터니 리처드 호어'가 개발한 정렬 알고리즘이다. 
2. 퀵 정렬은 불안정 정렬에 속한다.
3. Pivot이라는 값을 이용하여 다른원소와 비교한 후에 정렬한다.
4. Merge Sort에 비교해 보았을때 pivot값을 이용하기 때문에 비균등하게 분할한다.
5. 분할 정복 방법중에 하나이다.


### Quick Sort의 과정


1. 리스트 안의 한 요소를 먼저 선택한다. 그 값을 pivot이라 치앟ㄴ다.
2. low와 high값을 각각 pivot바로 오른쪽 값, 리스트의 맨오른쪽 값으로 이용한다.
3. low는 리스트의 마지막 값, high는 piovt값 다음의 값을 향하여 이동한다. 예시에서는 ++, --로 표현한다.
4. low와 high가 이동중에 pivot이 위치한 리스트 값과 각각의 index의 리스트 값을 계속하여 비교해 나간다.
5. low인덱스의 리스트 값이 high인덱스의 리스트 값보다 크면 swap을 시킨다.
6. 위의 과정들이 low가 high 값보다 작을때 계속하여 움직이면서 비교한다.


### Quick Sort의 특징


1. 시간 복잡도가 O(nlogn)으로 다른 정렬 알고리즘과 비교해 보았을때 가장 빠르다.
2. 정렬된 리스트에서는 퀵 정렬의 불균등 분할의 수행시간이 더 많이 걸린다. 최악일때 시간복잡도가 N^2이다.


아래 예시는 필자가 Java를 이용하여 퀵소트를 구현한 것이다. 중간중간의 설명을 넣어 놓았기 때문에 이해가 잘 될 것이다.


```kotlin


public class MyQuickSort {

    public MyQuickSort() {
        int[] input = {1,3,-1,2,-5,3,7,9,11};
        quick_sort(input, 0, input.length-1);
        printArr(input);
    }
    
    public static int partition(int[] list, int left, int right) {
        
        int temp;
        int low = left+1;
        int high = right;
        
        do {
            
            // 왼쪽부터 pivot보다 큰값을 찾는 과정 : low -->
            while(low<=right && list[low]<list[left]) {
                low++;
            }
            
            // 오른쪽부터 pivot보다 작은값을 찾는 과정 <-- high
            while(high>=left && list[high]>list[left]) {
                high--;
            }
            
            //low, high 교차하지 않았을때
            if(low < high) {
                temp = list[high];
                list[high] = list[low];
                list[low] = temp;
            }
            
        } while(low < high);
        
        // pivot값을 정렬하기
        temp = list[left];
        list[left] = list[high];
        list[high] = temp;
        
        return high;
    }
    
    public void quick_sort(int[] list, int left, int right) {
        
        if(left<right) {
            // q가 pivot이 되어 정렬되어있는 상태!
            int q = partition(list, left, right);
            quick_sort(list, left, q-1);
            quick_sort(list, q+1, right);
        }
    }
    
    public void printArr(int[] list) {

        for(int item : list) {
            System.out.print(item + " ");
        }
    }
}

```

