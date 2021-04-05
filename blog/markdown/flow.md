
```flowchart
start=>start: 开始
input=>inputoutput: 输入
operation=>operation: 操作
condition=>condition: 操作出错？
output=>inputoutput: 输出
error=>operation: 请重新输入
end=>end: 结束

start->input
input->operation
operation->condition
condition(no,bottom)->output
condition(yes)->error(top)->input
output->end
```

```mermaid
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->>John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail!
    John-->>Alice: Great!
    John->>Bob: How about you?
    Bob-->>John: Jolly good!
```