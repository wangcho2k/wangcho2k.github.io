---
title:  "나무위키 덤프 활용하기"
date:   2022-06-02 14:17:40 +0900
categories: python json namuwiki
---


참고: [파이썬으로 나무위키 JSON 덤프 데이터 파싱하기
](https://heegyukim.medium.com/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%EB%82%98%EB%AC%B4%EC%9C%84%ED%82%A4-json-%EB%8D%A4%ED%94%84-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8C%8C%EC%8B%B1%ED%95%98%EA%B8%B0-8f41cee1e155)

## 대용량 JSON 읽기

덤프된 [나무위키](https://namu.wiki) 데이터 JSON은 한 줄에 한 레코드가 저장되는 `jsonl` 포맷이 아닌 한 줄에 모든 데이터가 다 수록되어 있는 형태이기 때문에 `readline()` 및 `json` 패키지 등으로 읽어들이는 방법으로는 처리가 불가능하고 실행 도중 메모리 에러 등을 일으킵니다.  
(마찬가지 이유로 `vim` 등의 편집기로 미리보기도 어렵습니다.)

따라서 line-by-line 이 아닌 방법으로 처리하는 parser 로 처리할 필요가 있으며 `ijson` ([PyPI](https://pypi.org/project/ijson/)) 가 여기에 부합합니다.   
`ijson` 은 iterative 한 parser 로서 내부를 정확하게 들여다 보지는 않았지만 character (혹은 byte-sequence) 단위로 처리하는 것으로 생각됩니다.   
일반적인 방법으로는 열리지 조차 않는 수 GB 이상의 json 파일 내용을 순차적으로 처리할 수 있습니다.

{% highlight python %}
def parse_namuwiki_json(limit = -1, debug=False):
  i = 0
  doc = {}
  with open(SOURCE_FILE) as f:
    for prefix, event, value in ijson.parse(f):

      if debug:
        print(prefix, event, value)

      if (prefix, event) in capture_values:
        doc[prefix[5:]] = value
      if (prefix, event, value) == ("item", "end_map", None):
        yield doc
        doc = {}
        i += 1

        if limit > 0 and i >= limit:
          break
{% endhighlight %}

위 예제는 `parse()`  메서드를 이용해 순차적으로 파싱하는 코드입니다. 이 iterator 는 `prefix, event, value` 를 값으로 내주게 되며,   
`name: value` 구조의 JSON 을 읽을 때 `prefix` 는 name, `value` 는 말 그대로 value에 해당되죠.


다만 iterative 즉 덩어리로 읽는 것이 아닌 순차적으로 읽어나가기 때문에 hierarchical 하게 구성된 JSON 을 읽을 때에는 실제 원하는 leaf node 쪽의 (예를 들어 가장 자식 쪽) 정보를 읽을 때와 같은 상황에서는 주의가 필요합니다.   

이 때 필요한 것이 `event` 부분인데 현재 parser 가 어느 부분을 읽고 있는지를 나타내주게 되죠.
`map_key` 일 경우에 실제 name 에 해당되는 value 를 물고 있는 것이며, `start_map` 이나 `end_map`  은 각각 JSON 객체의 시작과 끝에 해당됩니다.  
즉 `end_map` 을 통해 해당 항목을 다 읽었음을 감지할 수 있으며 위 코드에서도 이를 통해 한 document 를 yield 하는데 쓰고 있습니다.

## 필터링

필터링 부분은 위 예제를 그대로 사용했는데, wiki 문법으로 된 데이터의 텍스트만을 끄집어내는 것이라 할 수 있겠습니다.

## jsonl 로 변환하기

parser 를 통해 얻어진 JSON 객체를 한 줄씩 저장하면 됩니다 🙂

{% highlight python %}
with open(OUT_FILE, "w") as out:
  for i, doc in enumerate(parse_namuwiki_json()):
    text = doc['text']
    text = clean_text(text)

    doc['text'] = text
    out.write(json.dumps(doc))
    out.write("\n")

    if (i % 100) == 0:
      print("progress: ", i)
{% endhighlight %}


