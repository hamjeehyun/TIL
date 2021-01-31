#### Linked List 선언
```
class Node:
    def __init__(self, item):
        self.data = item
        self.next = None

class LinkedList:
    def __init__(self):
        self.nodeCount = 0
        self.head = None
        self.tail = None
```

#### (07) 연결 리스트 순회

제 7 강에서 소개된 추상적 자료구조로 LinkedList 라는 이름의 클래스가 정의되어 있다고 가정하고, 이 **리스트를 처음부터 끝까지 순회**하는 메서드 traverse() 를 완성하세요.

메서드 traverse() 는 리스트를 리턴하되, 이 리스트에는 연결 리스트의 노드들에 들어 있는 데이터 아이템들을 연결 리스트에서의 **순서와 같도록** 포함합니다. 예를 들어, LinkedList L 에 들어 있는 노드들이 43 -> 85 -> 62 라면, 올바른 리턴 값은 \[43, 85, 62\] 입니다.

이 규칙을 적용하면, 빈 연결 리스트에 대한 순회 결과로 traverse() 메서드가 리턴해야 할 올바른 결과는 \[\] 입니다.

```
	def traverse(self):
		result = []
		curr = self.head
		while curr.next:
			curr = curr.next
			result.append(curr.data)
		return result

```

#### (08) 연결 리스트 노드 삭제

제 8 강에서 소개된 추상적 자료구조 LinkedList 클래스의 메서드로서 popAt() 메서드를 강의 내용에 소개된 요구조건을 만족시키도록 구현하세요.

초기 코드로 들어 있는 것은 solution() 함수를 포함하여 다른 부분은 수정하지 말고, def popAt(self, pos): 의 메서드 몸체만 구현하세요.

만약, 인자로 주어진 pos 가 올바른 범위의 값을 가지지 않는 경우에는 IndexError exception 을 발생시키도록 합니다. 이렇게 하기 위한 코드는 raise IndexError 입니다.

```
    def popAt(self, pos):
        if pos < 1 or pos > self.nodeCount + 1:
            return False

        if pos == 1:
            curr = self.getAt(pos)
            self.head = curr.next;

        else:
            if pos == self.nodeCount + 1:
                prev = self.getAt(pos-1)
                prev.next = self.tail;
            else:
                prev = self.getAt(pos-1)
                curr = self.getAt(pos)
                prev.next = curr.next

        self.nodeCount -= 1
        return self.getAt(pos)

```

#### dummy head 를 가지는 연결 리스트


#### (09) dummy head 를 가지는 연결 리스트 노드 삭제

문제 설명

제 9 강에서 소개된 추상적 자료구조LinkedList는 dummy head node 를 가지는 연결 리스트입니다. 이 클래스의 아래와 같은 메서드들을, 강의 내용에 소개된 요구조건을 만족시키도록 구현하세요.

popAfter() popAt()

이 때,popAt()메서드의 구현에서는popAfter()를 호출하여 이용하도록 합니다. (그렇게 하지 않을 수도 있지만, 여기에서는popAfter()의 이용에 의해서 코드 구현이 보다 쉬워지는 것을 확인하기 위함입니다.)

초기 코드로 들어 있는 것은solution()함수를 포함하여 다른 부분은 수정하지 말고,def popAfter(self, prev):와def popAt(self, pos):의 메서드 몸체만 구현하세요.

만약,popAt()메서드에 인자로 주어진pos가 올바른 범위의 값을 가지지 않는 경우에는IndexErrorexception 을 발생시키도록 합니다. 이렇게 하기 위한 코드는raise IndexError입니다.

```
    def popAfter(self, prev):
        curr=prev.next
        if prev.data==None:
           prev.next=None
        if curr.next == None:
            self.tail=prev
            prev.next=None
        else:
            prev.next=curr.next      
        self.nodeCount -=1
        return curr.data

    def popAt(self, pos):
        if pos<1 or pos>self.nodeCount:
            raise IndexError
        else:
            prev=self.getAt(pos-1)
            return self.popAfter(prev)
```
