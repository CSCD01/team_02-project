# Marks

### Deliverable 0

| Criteria | Grade |
| :------- | :-----: |
| Intro Team (5) | 3 | 
| Intro Members (12) | 12 |  
| Agreement (6) | 6 |  
| Meal Pic (2) | 2 |  
| Presentation (10) | 10 | 

**Total (35)**: 33  
**Comments**:  

### Interview 0

| Question | Grade |
| :------- | :-----: |
| Intro Team (2) | 2  |
| Agreement (2) | 2 |
| Choosing Project (2) | 2 |  
| Choice of Project (2) | 2 |  
| Expectations (2) | 2 |  

**Total (10)**: 10  
**Comments**:  

----------------

### Deliverable 1 (Remarked)

| Criteria | Grade |
| :------- | :-----: |
| Architecture (15) | 4 |  
| Software Process (15) | 15 |  
| Presentation (10) | 10 |  

**Total (40)**: 29  
**Comments**:  Proper architecture diagram not present  

### Interview 1

| Question | Grade |
| :------- | :-----: |
| Overall (2) | 0 |  
| 2 Components (2) | 0 |  
| Cool (2) | 1 |  
| Explain (2) | 2 |  
| Why (2) | 1 |  

**Total (10)**: 4  
**Comments**: Architecture diagram not clear enough to answer questions, it just shows model-view-template pattern of Django. Failed to mention interesting design in the project.    

-----------------

### Deliverable 2

| Criteria | Grade |
| :------- | :-----: |
| Bugs Intro (10) | 10 |  
| Choice of bugs (5) | 5 |  
| Correctness (25) | 17 |  
| Documentation (10) | 10 |
| Test Suite (10) | 5 | 
| Software Process (25) | 25 |
| Presentation (15) | 15 |

**Total (100)**: 87  
**Comments**: #12842 need to fix lint issues to have checks pass and project maintainer said it was wrong. #13508 needed testing. The deliverable was written very well, and great job explaining how the kanban board was used and issues faced.  

### Interview 2

| Question | Grade |
| :------- | :-----: |
| Summary (1) | 1 |  
| Demo 1 (2) | 2 |  
| Explain 1 (1) | 0 |  
| Test (2) | 1 |  
| Demo 2 (2) | 2 |  
| Software Process (2) | 2 | 
| Bonus (1) | 1 |

**Total (10)**: 9   
**Comments**: Not all members knew the bug, and therefore explanation was not great. Not everyone knows how the project is tested  

-----------------

### Deliverable 3

| Criteria | Grade |
| :------- | :-----: |
| Issue Description (10) | 10 |  
| Implementation Plans (10) | 10 |  
| Test (5) | 5 |  
| Architecture (10) | 3 |
| Presentation (10) | 10 |  

**Total (45)**: 38   
**Comments**: Significant imporvement on architecture diagrams from deliverable 1, well done. But this deliverable needed to go into more depth about the archtiecture. It should've also talked about the following:

```txt
-- architectural style; architectural pattern if applicable
-- description of all components
-- description of interfaces between components
-- discussion of level of coupling between components
-- discussion of cohesion of components
-- how are each of the following addressed with architectural choices (if applicable)
   -- performance
   -- security
   -- safety
   -- availability
   -- maintainability
```

### Interview 3

| Question | Grade |
| :------- | :-----: |
| First Feature (2) | 2 |  
| Second Feature (2) | 2 |  
| Explain Reasoning (1) | 1 |  
| Testing (2) | 2 |  
| Architecture (3) | 3 |  

**Total (10)**: 10   
**Comments**:   

-----------------

### Deliverable 4

| Criteria | Grade | Comments
| :------- | :-----: | :------ | 
| Implementation (40) | 29 | Full marks for correctness. Lost marks for quality of code. Main issues: linting caused Travis check to fail, issues with .gitignore |   
| Testing (20) | 20 | | 
| User Guide(s) (10) | 10 | |  
| Design Document (10) | 10 | | 
| Process (20) | 10 |  | 
| Compared with other teams (10) | ? | |   
| Presentation (10) | 10 |  |

**Total (120)**: 89 + ?      
**Comments**: Introduced new files to .gitignore, package-log.json is useful and should not be ignored. This file is not included in the project so having it in .gitignore in this scenario is not a huge deal  
src/olympia/ratings/views.py line # 157 does not need "is True".   
I dont like {1: "up_vote", 0: "down_vote", -1: "cleaned_vote"}, I think {1: "up_vote", 0: "cleaned_vote", -1: "down_vote"} is more intuitive. No marks deducted for this.   
Travis checks failed because of linting issues. Test case test_soft_delete in src/olympia/ratings/tests/test_models.py failed, but I think this was an existing issue in the project.    
The maintainers should have been contacted before work was started on this issue. Unluckily, since this was not done, a maintainer closed this issue a day after the first commit for this issue was made by the team.     
There is one proper pull request: https://github.com/CSCD01/addons-server-team02/pull/2 after that all commits seemed to be made directly to the main branch. On the pull request, all three coding members for this deliverable seemed to work at the same time. No marks deducted for this but in the future use the github features to do code reviews. Reviewers can be assigned top right corner of pull request page.


### Interview 4

| Question | Grade |
| :------- | :-----: |
| Feature Description (2) | 2 |  
| Technical Explanation (2) | 2 |  
| Architecture (2) | 2 |  
| Testing (2) | 2 |  
| Software Process (2) | 2 |  

**Total (10)**: 10   
**Comments**:   

