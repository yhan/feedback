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
   
   *what kind of time window exigence encourge us to use one or another?*
   
1. When you send run command to your write API. What's next?
   - Write API give (REST) resource, give chance for UI to poll?
   - Command executing extremly fast (<1s), status feedback directly?
   
1. API design.
   Something like: https://api.projects.lokad/projects/{project-id}/run OR
   https://api.projects.lokad/run-project (with query parameter OR with payload including project id) ?
   
   *Know that a project is a script*  
   What if I have another screen, in which for a given project, I can Run the script. This action combines both `SaveScript` and `Run` actions.
   Would you expose your API in REST style or RPC style? Explain your understanding of REST vs RPC.
1. I have to pipes: 1) Write API return result. 2) Polling result
   What kind of problem will I have?
   For just one click on Run button, possible to have project status transition: ready -> running (cancellation button activated) -> ready ->  running (cancellation button activated).


