1.粗糙的需求

2.向我提问，想我提问，一次一个问题，确保我们理解一致。

3.生成具体需求和功能文档， 文档化。

b：架构设计

生成架构设计文档，+可视化架构图
LLM 外接 mcp生成图，也可以生成markdwon，markdown momo插件生成图


c 代码生成
1.架构设计生成
2.项目构建任务 任务步骤 针对每个任务生成promot（prompt-> prompt）


todo 要写下来

cdk 或者 cloud formatiion的方式






prompt-> prompt
大型人物要生成todo list  避免中断，重新生成消耗token

prompt 容错，  

分析，准备怎么做给我讲，我决定了再做








mcp：模型上下文协议

prompt + mcp 业务创新性  极大提升

ai自主管理的模式， vs ai辅助开发模式





agentic ai


gen ai 

human：

agentic ai：主动性



总结：

0. ai-ask 人回答 确保理解一致

1. prompt——> prompt 然后我再调整这个prompt
2. todo写下来，对于消耗很大的工作，要用todo写下来，避免从头再来。也是ai自己任务清单。
3. 包括跟ai讨论的需求，设计，prompt都要保留为文档，这样将来可以重建session来处理问题。
3，代码写注释，这样后续有问题把注释给ai就好了，而不是ai看代码反推业务逻辑
4.prompt 容错,分析，准备怎么做给我讲，我决定了再做,如果一味的压榨ai，它有对策，但是都是骚操作。

5.mcp是啥
6.



def aggregate_slices_by_similarity(self, docs_idx, vecs, threshold):
        start = self._current_time_()
        aggregated_slices = [[docs_idx[0]]]
        sents_similarity = []
        sum = 0.0
        for i in docs_idx[1:]:
            similarity_score = np.dot(vecs[i], vecs[i - 1])
            sum += similarity_score
            sents_similarity.append(similarity_score)
            if similarity_score < threshold:
                aggregated_slices.append([])
            aggregated_slices[-1].append(i)
        if len(sents_similarity) > 0:
            score_avg = sum / len(sents_similarity)
        end = self._current_time_()
        return aggregated_slices
