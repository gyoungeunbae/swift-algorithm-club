# Trees

Tree는 오브젝트 간의 계층 관계를 나타냅니다. Tree 구조를 나타낸 그림입니다.

![A tree](Images/Tree.png)

보통 Tree는 Node들로 구성이 되고, 각각의 Node들은 서로 Link되어 있습니다.
Node들은 그들의 자식 Node(ChildrenNode)와도 연결되어 있고, 보통 부모 Node(Parent Node)와도 Link되어 있습니다. 자식 Node(ChildrenNode)들은 아래의 그림에서 밑에 부분의 해당하는 Node들입니다. 부모 Node(Parent Node) 는  위쪽의 해당하는 Node입니다. Node는 항상 단 하나의 부모를 갖지만 여러 개의 자식Node(ChildrenNode)들 을 가질 수 있습니다.

![A tree](Images/ParentChildren.png)

부모 Node(Parent Node)가 없는 Node는 *Root Node* 입니다, 자식 Node(ChildrenNode)가 없는 Node는 *Leaf Node* 입니다. 
Tree에서는 Node 간에 Pointer(<->)를 Cycle을 형성하지 않습니다. 아래의 그림 같은 경우에는 올바른 Tree 구조 가 아닙니다. 


![Not a tree](Images/Cycles.png)


자료구조의 종류 중 하나인 그래프(Graph)와 같이, 실제로 Tree는 간단한 형태의 Graph입니다. 
이 문서에서는 일반적인 Tree에 관해 설명합니다. 다음의 Tree는 각 Node가 가질 수 있는 자식의 수 또는 Tree의 Node 순서에 대한 제한이 없는 트리입니다.

Swift의 code에서 Tree의 기본 구현은 다음과 같습니다.

```swift
public class TreeNode<T> {
  public var value: T

  public var parent: TreeNode?
  public var children = [TreeNode<T>]()

  public init(value: T) {
    self.value = value
  }

  public func addChild(_ node: TreeNode<T>) {
    children.append
    node.parent = self
  }
}

TreeNode 의 구성을 간단히 설명하면 일반적인 `T` 타입위 값(Value) , `Parent` Node 에 대한 참조, 그리고 자식 Node (Children Node) 위 배열을 가지고 있습니다.
Tree 안에 내용을 확인할 수 있는 Description Method를 추가하면 이해하는 데 더 유용 할 것입니다.


```swift
extension TreeNode: CustomStringConvertible {
  public var description: String {
    var s = "\(value)"
    if !children.isEmpty {
      s += " {" + children.map { $0.description }.joined(separator: ", ") + "}"
    }
    return s
  }
}
```

Playground 에서이 tree의 동작들을 확인해 보세요.

```swift
let tree = TreeNode<String>(value: "beverages")

let hotNode = TreeNode<String>(value: "hot")
let coldNode = TreeNode<String>(value: "cold")

let teaNode = TreeNode<String>(value: "tea")
let coffeeNode = TreeNode<String>(value: "coffee")
let chocolateNode = TreeNode<String>(value: "cocoa")

let blackTeaNode = TreeNode<String>(value: "black")
let greenTeaNode = TreeNode<String>(value: "green")
let chaiTeaNode = TreeNode<String>(value: "chai")

let sodaNode = TreeNode<String>(value: "soda")
let milkNode = TreeNode<String>(value: "milk")

let gingerAleNode = TreeNode<String>(value: "ginger ale")
let bitterLemonNode = TreeNode<String>(value: "bitter lemon")

tree.addChild(hotNode)
tree.addChild(coldNode)

hotNode.addChild(teaNode)
hotNode.addChild(coffeeNode)
hotNode.addChild(chocolateNode)

coldNode.addChild(sodaNode)
coldNode.addChild(milkNode)

teaNode.addChild(blackTeaNode)
teaNode.addChild(greenTeaNode)
teaNode.addChild(chaiTeaNode)

sodaNode.addChild(gingerAleNode)
sodaNode.addChild(bitterLemonNode)
```

`tree`의 값을 Print out 해보면 다음의 값들이 출력되는 것을 확인할 수 있습니다. 


	beverages {hot {tea {black, green, chai}, coffee, cocoa}, cold {soda {ginger ale, bitter lemon}, milk}}



다음 과 같은 구조를 따릅니다.

![Example tree](Images/Example.png)

The `beverages` Node는 부모 Node(Parent Node)를 가지고 있지 않기 때문에 Root Node에 해당합니다.
`black`, `green`, `Chad`, `coffee`, `cocoa`, `ginger ale`, `bitter lemon`, `milk` 등은 자식 Node(ChildrenNode)들을 가지고 있지 않기 때문에 Leaves Node에 해당 됩니다.


모든 Node들에서 `parent` 속성을보고 이용하여 루트 로 돌아갈 수 있습니다.

```swift
teaNode.parent           // this is the "hot" node
teaNode.parent!.parent   // this is the root
```

보통 Tree에 관하여 정의 할때 다음의 같은 개념들이 설명을 뒷받침 됩니다.

- **Height of the tree 트리의 높이.**  : 상위(Root) Node 와 가장 낮은 leaf 사이의 링크(link) 수입니다. 다음의 예제에서 Tree의 높이는 Root에서 최하단 Node 까지 3 번 점프하기 때문에 높이는 3 이 됩니다.

- **Depth of a node Node의 깊이.** : Node와 RootNode 사이의 링크(Link) 수입니다. 예를 들어 'Tea'의 깊이는 루트에 도달하기 위해 두 번 점프해야하기 때문에 2입니다. (루트 자체의 깊이는 0입니다.)


Tree를 구성 하는 데에는  여러 가지 방법이 있습니다. 예를 들어, 때로는`parent` 속성을 전혀 가질 필요가 없습니다. 또는 각 Node에 대하여  최대 두 개의 하위 Node 만 구성 하면 됩니다. 이러한 타입의  트리는 (../ Binary Tree /) 라고합니다. 매우 일반적인 유형의 트리는  (../ Binary Search Tree /) (또는 BST) 는 
검색 속도를 높이기 위해 특정 방식으로 Node를 정렬하는 이진  엄격한 버전의 Tree 입니다.
범용적으로 사용되는 Tree 는 계층 적 데이터를 설명하는 데 유용하지만 실제로 애플리케이션에 사용되는 Data 구조나 유형에 따라 Tree구조의 적합성이 결정 됩니다.


다음은`TreeNode` 클래스를 사용하여 트리가 특정 값을 포함하고 있는지 판단 할 수있는 예제입니다.  먼저 노드(Node) 자체의 'value'속성을 살펴 봅니다. 일치하는 게 없다면 차례대로 자식 노드(ChildrenNode)들을 살펴 보게됩니다. 물론, 자식 노드(ChildrenNode)들도 `TreeNodes`이기 때문에 재귀 적으로 같은 과정을 반복 할 것입니다. 우선 자신의 값을  먼저 확인 해 본다음  다음 자식 노드(ChildrenNode)들을 확인합니다 . 그리고 그들의 자식 노드(ChildrenNode)들을 똑같은 일을 다시 할 것입니다. 재귀동작과 와 Tree와의 상호 작용 입니다.


코드는 다음과 같습니다.

```swift
extension TreeNode where T: Equatable {
  func search(_ value: T) -> TreeNode? {
    if value == self.value {
      return self
    }
    for child in children {
      if let found = child.search(value) {
        return found
      }
    }
    return nil
  }
}
```

Tree를 사용 하는 방법에 대한 에입니다.

```swift
tree.search("cocoa")    // returns the "cocoa" node
tree.search("chai")     // returns the "chai" node
tree.search("bubbly")   // nil
```


배열을 사용하여 Tree를 설명하는 것도 가능합니다.  배열의 인덱스는 서로 다른 Node 간의 링크를 만듭니다. 예를 들어, 우리는 :

	0 = beverage	5 = cocoa		9  = green
	1 = hot			6 = soda		10 = chai
	2 = cold		7 = milk		11 = ginger ale
	3 = tea			8 = black		12 = bitter lemon
	4 = coffee				


다음과 같이 배열로 Tree를 설명 할 수 있습니다.

	[ -1, 0, 0, 1, 1, 1, 2, 2, 3, 3, 3, 6, 6 ]


배열의 각 항목은 상위 Node에 대한 포인터입니다. 인덱스 3의 항목 인`tea`는 부모(ParentNode)가`hot '이기 때문에 값 1을가집니다. Root Node는 부모(ParentNode)가 없으므로`-1`을 가리 킵니다. Root Node 로 돌 아 갈 수있지만다른 방법으로는 돌아갈 수 없습니다.



그런데 때로는 * forest *라는 알고리즘을 보게됩니다. 이것은  별도의 트리 객체 모음에 주어진 이름입니다. 이에 대한 예는 [union-find] (../ Union-Find /)를 참조하십시오.

*Written for Swift Algorithm Club by Matthijs Hollemans*
 -  [*Translated  by Derrick](superbderrick.github.io)


