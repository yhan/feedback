# LOKAD White board interview 
Requirements:

A Dashboard

## General purpose:
We have projects, each project can be run. Run them, have feedbacks: running => failed OR ready (success and ready for a next run).

## GUI
A web interface:  
A list of projects, button Run and Cancel(Enabled only when project is running), Status column.

## Already implemented:
Command handling, read model projecting CQRS system.

## Open questions:
How whould design this system?

## Interesting questions:
1. Should we use reactive notification including projecting run status transitions to feed UI?
   1. Polling
   1. Long polling
   1. Web socket
   1. Server sent event
1. When you send run command to your write API. What's next?
   - Write API give (REST) resource, give chance for UI to poll?
   - Command executing extremly fast (<1s), status feedback directly?
1. API design.
   Something like: https://api.projects.lokad/projects/{project-id}/run OR
   https://api.projects.lokad/run-project (with query parameter OR with payload including project id) ?
   
   *a project is a script*
   What if I have another screen, in which for each project, I

