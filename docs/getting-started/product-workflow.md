This guide details how Code4Community teams take a product from an idea to reality. It outlines the requirements for communication and work between designers, developers, and product managers. 

## Initial Partner Meetings

* Meet with the client (partners, stakeholders) to discuss what issues they are currently facing. 
* Create a Project Requirements Document (PRD), a shared document for all interested parties - the clients, the product manager, the designers, and the develoeprs. This contains as much detail as possible about the product goal. 
    * What problem are they facing?
    * What is the solution the client wants? 
    * How can we turn that solution into a reality?
    * What reality are we building towards in a product?
* Product team meets to outline what the minimum viable product (MVP) is for a Release, deciding what features to prioritize and which to leave for later releases.
    * Detailed sitemap of all pages that will need to be built, the functionality on each of those pages, and the navigation between them 
    * Descriptions of the advanced functionality contained within the release
    * Initial architecture proposals for how the code will be structured 
* Split the PRD into Milestones - sets of work and tickets for design and development, each of which centers around a clear goal which impacts the end user's experience
* Designers begin wireframing pages and share them as they are completed. Screenshots and links to the designs are added to the PRD and partners are notified to view them. 
* Meet with the partners to review the PRD and any other materials relevant to the product. Assuming there is clear understanding of features between the partners, designers, product manager, developers, it is time to move on to making tickets. 

## Writing Milestones and Tickets

* The product manager, technical lead, product designer, and any other interested team members meet to conduct a Sprint "Pre-Grooming", in which the team works to write small, actionable tickets based on the mutual decisions made in the PRD. A given ticket should have: 
    * Acceptance Criteria - concise description of what work must be completed for the ticket to be considered "done"
    * Description - longer form background, context, and implementation details regarding the ticket to help developers understand how the ticket fits our goals. 
    * Design - Frontend tickets should have a screenshot of the page or component design, as well as a link to Figma or similar developer exports. 
    * Verification Steps - Does this change involve large feature changes? If so, we must describe how to verify functionality both before and after these changes go into effect. 
* Once a large body of tickets are ready to groom, the whole team comes together to discuss the need, context, and goals of each ticket. Every team member should be given the opportunity to ask questions, so that everyone understands each ticket as much as possible. Tickets are then assigned Story Points, which are meant to gauge the level of difficulty. 
    * Story Points - we use Fibonacci numbers to story point our tickets (1, 2, 3, 5, 8). One teammate will count down, then all developers put a number of points up with their fingers. If there is a clear consensus, the ticket is assigned that number of story points. If not, the team discusses further and arrives at a consensus.  
* After a grooming meeting, the team conducts a Sprint Planning and decides which tickets are to be included in a given sprint. There is a mutual understanding that tickets pulled into a sprint should be completed by the end of the sprint, and if unexpected challenges come up clear communication is required so that the team can ensure all work is done when required. All tickets in the sprint must be clearly actionable and not blocked by other tickets. 

## Developer Code Review Workflow

* When a developer finishes work on a ticket, they create a Pull Request on GitHub with the changes. The code is reviewed and once it is approved, if it's a frontend change it requires the product designer's approval. This is done either via Storybook or with review applications. When design has approved any frontend change, the developer should then follow any verification steps prior to releasing changes to production. For large features, the product manager or designer should conduct QA too, but the developer is still responsible for verifying themselves before sending to someone else. 