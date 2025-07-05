---
Name: HTML Traversing
---

# HTML Traversing

Traversing allows you to traverse through an HTML node.

## Properties

| Name | Description |
| :--- | :---------- |
| [ChildNodes](child-nodes) | Gets all the children of the node. |
| [FirstChild](first-child) | Gets the first child of the node. | 
| [LastChild](last-child) | Gets the last child of the node. |
| [NextSibling](next-sibling) | Gets the HTML node immediately following this element. |
| [ParentNode](parent-node) | Gets the parent of this node (for nodes that can have parents). |


## Methods

| Name | Description |
| :--- | :---------- |
| [Ancestors()](ancestors) | Gets all the ancestors of the node.  |
| [Ancestors(String)](ancestors#public-ienumerable--htmlnode--ancestorsstring-name) | Gets ancestors with a matching name. |
| [AncestorsAndSelf()](ancestors-and-self) | Gets all ancestor nodes and the current node. |
| [AncestorsAndSelf(String)](ancestors-and-self#public-ienumerable--htmlnode--ancestorsandselfstring-name) | Gets all ancestor nodes and the current node with a matching name. |
| [DescendantNodes](descendant-nodes) | Gets all descendant nodes for this node and each of the child nodes |
| [DescendantNodesAndSelf](descendant-nodes-and-self) | Returns a collection of all descendant nodes of this element, in document order |
| [Descendants()](descendants) | Gets all descendant nodes in enumerated list |
| [Descendants(String)](descendants#public-ienumerable--htmlnode--descendantsstring-name) | Gets all descendant nodes with a matching name |
| [DescendantsAndSelf()](descendants-and-self) | Returns a collection of all descendant nodes of this element, in document order |
| [DescendantsAndSelf(String)](descendants-and-self#public-ienumerable--htmlnode--descendantsandselfstring-name) | Gets all descendant nodes including this node |
| [Element](element) | Gets the first-generation child node with a matching name |
| [Elements](elements) | Gets the first-generation child nodes with a matching name |
