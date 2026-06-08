# Quick Digest Pipeline

## Lineage
- Input: topic from user request (passed by ORCHESTRATOR.md)
- Output: ../workspace/deepseek/quick_[topic].md

## Steps
1. Find 3 key facts about the topic
2. Write one paragraph per fact (50-80 words each)
3. At the top of the output file write:
   > Routed by: ORCHESTRATOR.md → quick_digest.md
   > Topic: [topic]
4. Save to ../workspace/deepseek/quick_[topic].md

## Constraints
- Maximum 300 words total
- No web search, no URLs
- Exact output filename only
