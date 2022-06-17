---
title: Agile sprint calendar
author: ianpowell
date: 2019-08-03 18:33:00 +0100
categories: [working]
tags: [agile]
mermaid: true
---

## Example Diary

```mermaid
gantt
    title Wednesday 3rd July 2019 - Tuesday 16th July 2019

    section Sprint
    Week 1 (3rd July - 9th July)    :a1, 2019-07-03, 1w
    Story refinement        :crit, st1, 2019-07-01, 1h
    Previous sprint review  :active, sprintreview1, 2019-07-03, 1h
    Retrospective           :active, retrospective1, after sprintreview1, 1h
    Sprint planning         :active, sprintplanning1, after retrospective1, 1h
    Story refinement        :crit, st2, 2019-07-04, 1h

    Story refinement        :crit, st3, 2019-07-08, 1h
    Week 2 (10th July - 16th July)  :b1, 2019-07-10, 1w
    Story refinement        :crit, st4, 2019-07-11, 1h
    Story refinement        :crit, st4, 2019-07-15, 1h

    Previous sprint review  :active, sprintreview2, 2019-07-17, 1h    
    Retrospective           :active, retrospective2, after sprintreview2, 1h    
    Sprint planning         :active, sprintplanning2, after retrospective2, 1h
```

| Week | Monday        | Tuesday | Wednesday                                               | Thursday      | Friday |
|------|---------------|---------|---------------------------------------------------------|---------------|--------|
| One  | Refinement 1h |         | Sprint Review 30m<br/>Retrospective 30m<br/>Sprint Planning 30m | Refinement 1h |        |
| Two  | Refinement 1h |         |                                                         | Refinement 1h |        |
