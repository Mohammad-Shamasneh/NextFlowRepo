# Focus, Training & Knowledge Assistant MCP

## Pitch

1_ Students and developers often lose useful information across AI conversations, email messages, training sessions, and note-taking applications.
2_ The Focus, Training & Knowledge Assistant MCP provides one workflow for collecting and organizing this information.
3_ It exposes tools that ask a language model to summarize the current conversation, save the result to Notion,
and summarize Gmail messages received during the last 24 hours. 
4_ The project also includes required tools for managing focus sessions, tracking training progress, and launching predefined activity profiles.s.

## User & Demo Story

During Demo Day, the user asks the MCP assistant, “Summarize our current conversation and save it to Notion.” 
The MCP calls `summarize_current_conversation`, which sends the conversation content to the client language model and receives a structured summary.
It then calls `save_summary_to_notion` and returns the created Notion page link. The user then asks, 
“Summarize my emails from the last 24 hours.” The MCP calls `summarize_recent_emails`, retrieves the relevant Gmail messages, 
asks the language model to summarize them, and returns a concise digest containing the important messages and required actions.

## Tool Inventory


tool_name: `summarize_current_conversation` |description: Sends the current conversation to the client language model and returns a structured summary. 
|inputs: `{ messages: Message[] }` |output (shape): `{ title: string, summary: string, key_points: string[] }` |priority: P0 


tool_name: `save_summary_to_notion` |description: Creates a Notion page containing a prepared conversation summary. 
|inputs: `{ title: string, summary: string, key_points: string[] }` |output (shape): `{ page_id: string, page_url: string, saved_at: string }` |priority: P0 


tool_name: `summarize_recent_emails` |description: Retrieves Gmail messages received during the last 24 hours and asks the model to summarize them.
|inputs: `{ unread_only?: boolean }` |output (shape): `{ email_count: number, summaries: EmailSummary[], action_items: string[] }` |priority: P0 


tool_name: `start_focus_session` |description: Starts a study or training session and records its topic and start time. 
|inputs: `{ topic: string, profile_name?: string }` |output (shape): `{ session_id: string, started_at: string }` | priority: P1 


tool_name: `end_focus_session` |description: Ends an active focus session and calculates its duration. |inputs: `{ session_id: string }` 
|output (shape): `{ session_id: string, ended_at: string, duration_minutes: number }` |priority: P1 


tool_name: `launch_activity_profile` |description: Opens the approved applications and folders configured for an activity profile. 
|inputs: `{ profile_name: string }` |output (shape): `{ opened: string[], failed: string[] }` |priority: P1 


tool_name: `get_training_progress` |description: Returns a summary of focus and training sessions for a selected period. 
|inputs: `{ period: "day" | "week" : string }` | output (shape): `{ session_count: number, total_minutes: number, topics: string[] }` |priority: P1 


## Out of Scope

- Sending, replying to, deleting, or modifying Gmail messages.
- Automatically accessing Gmail or Notion without explicit authorization.
- Saving the complete conversation transcript to Notion; only the generated summary will be stored.
- A mobile application, web dashboard, or graphical user interface.
- Executing arbitrary terminal commands supplied by the user.
- Advanced habit streaks, charts, notifications, or calendar integration.

## Demo Day Success Criteria

- [ ] `summarize_current_conversation` returns a title, summary, and key points from a provided conversation.
- [ ] `save_summary_to_notion` creates a Notion page and returns a valid page link.
- [ ] `summarize_recent_emails` returns a digest of Gmail messages received during the previous 24 hours.

## Risks

### Risk 1: The MCP client may not support model sampling or may not provide the current conversation content

**Mitigation:** Test the selected MCP client and protocol version before implementing the complete workflow. 
The tool will accept conversation messages as input, and a fixture conversation will be prepared as a Demo Day fallback.

### Risk 2: Gmail or Notion authorization may block the live demonstration

**Mitigation:** Configure and test OAuth permissions early, use read-only Gmail access, 
and prepare local fixture emails and a mock Notion response as a fallback. The project will never send or delete email messages.