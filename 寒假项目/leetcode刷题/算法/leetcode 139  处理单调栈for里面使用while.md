```java
class Solution {

    public int[] dailyTemperatures(int[] temperatures) {

        int length=temperatures.length;

        int[] answer=new int[length];

        Deque<Integer> stack=new LinkedList<>();

        for(int i=0;i<length;i++){

            while(!stack.isEmpty() && temperatures[i]>temperatures[stack.peek()]){

                int day=stack.pop();

                answer[day]=i-day;

            }

            stack.push(i);

        }

        return answer;

    }

}

```