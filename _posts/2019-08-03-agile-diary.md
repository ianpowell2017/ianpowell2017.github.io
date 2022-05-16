---
title: Agile sprint calendar
author: ianpowell
date: 2019-08-03 18:33:00 +0100
categories: [agile]
tags: []
mermaid: true
---
## Diary
```mermaid
gantt
    title Wednesday 3rd July 2019 - Tuesday 16th July 2019

    section Sprint
    Week 1          :a1, 2019-07-03, 1w
    Week 2          :b1, 2019-07-10, 1w

    section Story refinement
    Mo - 1hr :crit, st1, 2019-07-01, 1h
    Th - 1hr :crit, st2, 2019-07-04, 1h
    Mo - 1hr :crit, st3, 2019-07-08, 1h
    Th - 1hr :crit, st4, 2019-07-11, 1h
    Mo - 1hr :crit, st4, 2019-07-15, 1h

    section Previous sprint review
    We - 30m :active, sprintreview1, 2019-07-03, 1h
    We - 30m :active, sprintreview2, 2019-07-17, 1h

    section Retrospective
    We - 30m :active, retrospective1, after sprintreview1, 1h
    We - 30m :active, retrospective2, after sprintreview2, 1h

    section Sprint planning
    We - 30m :active, sprintplanning1, after retrospective1, 1h
    We - 30m :active, sprintplanning2, after retrospective2, 1h
```

| Week | Monday        | Tuesday | Wednesday                                               | Thursday      | Friday |
|------|---------------|---------|---------------------------------------------------------|---------------|--------|
| One  | Refinement 1h |         | Sprint Review 30m<br/>Retrospective 30m<br/>Sprint Planning 30m | Refinement 1h |        |
| Two  | Refinement 1h |         |                                                         | Refinement 1h |        |

Monday Refinement 1 hour

Sprint
* Week 1 starts Wednesday 3rd July - 9th July
* Week 2 of sprint starts Wednesday 10th July - 16th July

