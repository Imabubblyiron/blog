# 분할정복 알고리즘 - Merge Sort



### Merge Sort의 개념


1. '존 폰 노이만'이라는 사람이 제인한 알고리즘이다.
2. 분할, 정복, 결합의 단계로 이루어지는 알고리즘이다.
3. 분할 정복 방법중에 하나이다.
4. 일반적인 방법에서는 안정 정렬에 속한다.


### Merge Sort의 과정


1. 우선 mergeSort()의 파라미터로 list와 ,첫번째 인덱스, 인덱스의 마지막 값을 넘겨주어 실행한다.
2. mid값을 기준으로 왼쪽과 오른쪽의 배열을 나누어 mergeSort를 계속해서 분리한다. 재귀이기 때문이다.
3. 위 과정이 끝에 도달한다면 루트노드 한개가 copy list에 저장될 것이다.
4. 이제 리스트의 값들을 각각 한개씩 분해하였으니 merge()를 순차적으로 실행할 시간이다.
5. 정렬된 값은 copy list에 인덱스에 맞게 저장되어 있기때문에 원래 list에 다시 저장해주도록 한다.
6. 재귀로 실행하였기 때문에 이 과정은 역순으로 진행되어 마지막에는 n/2개 n/2개가 오름차순에 맞게 merge될 것이다.
 

### Merge Sort의 특징


1. 안정적인 정렬 방법이고 시간 복잡도가 O(nlogn)으로 다른 정렬 알고리즘과 비교해 보았을때 가장 빠르편에 속한다.
2. 퀵 정렬과 다르게 최악일때도 시간복잡도가 NlogN이다.
3. 연결 리스트로 구성하면 제자리 정렬로 구현이 가능하다.
4. 큰 레코드를 정렬할 경우 어떤 정렬 방법보다 효율적이다.



아래 예시는 필자가 Java를 이용하여 병합 정렬을 구현한 것이다. 중간중간의 설명을 넣어 놓았기 때문에 이해가 잘 될 것이다.


```kotlin


public class MyMergeSort {

    public static int[] copy;
    
    public MyMergeSort() {
    
        int[] inputList = {1,4,2,39,11,1,3,4,-1,-99};
        copy = new int[list.length];
        mergeSort(inputList,0, inputList.length-1);
    }
    
    public static void merge(int[] list, int left, int middle, int right) {
        
        int i = left; // 배열에 할당된 후에 ++ 됨
        int j = middle+1; // 배열에 할당된 후에 ++ 됨
        int k = left; // k는 시작점부터의 인덱스, 시작점부터 순차적으로 넣기 때문에
        
        while(i<=middle && j<=right) {
        
            // ㅁㅁㅁㅁ 와 ㅁㅁㅁㅁ 중에 비교해서 copy에 넣음
            if(list[i]<=list[j]) {
                copy[k] = list[i];
                i++;
            } else {
                copy[k] = list[j];
                j++;
            }
            k++;
        }
        
        // XXXX 와 ㅁㅁㅁㅁ 상태 i는 middle을 넘어있음.
        if(i>middle) {
        
            for(int l = j; l<=right ; l++, k++ ) {
                copy[k] = list[l];
            }
        } else { // ㅁㅁㅁㅁ와 XXXX인 상태 i값은 여전히 middle값을 넘지않았음
                 // 다시 말해서 왼쪽 서브리스트에만 값들이 카피되지 않고 남아있음.
            for(int l = i; l<=middle; l++, k++ ) {
                copy[k] = list[l];
            }
        }
    
        for(int l = left; l<=right ; l++) {
        
            list[l] = copy[l];
        }
        
        System.out.println(Arrays.toString(list));
    }
    
    public static void mergeSort(int[] list, int left, int right) {
        
        int mid;
        
        if(left<right) {
        
            mid = (left+right)/2;
            mergeSort(list, left, mid);
            mergeSort(list, mid+1, right);
            merge(list,left,mid,right);
        }
    }
}

```

