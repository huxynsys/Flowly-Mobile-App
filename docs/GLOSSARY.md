Authority: decisions.md. If this file conflicts with decisions.md, decisions.md wins.

# Glossary

- **Task:** A user-owned unit of work with an intended duration and optional deadline; a Task can be placed in a Plan Block and can follow an MVP recurrence pattern.
- **Event:** A user-owned scheduled commitment with a defined time. A Fixed Event is a hard scheduling constraint and recurring fixed Events are post-MVP.
- **Plan Block:** A time-bounded placement of a Task or Event in a Plan Version, with a plan-item state.
- **Plan Version:** An immutable snapshot of a user's plan produced by plan generation or Replan; accepting a new Plan Version makes it active without erasing prior versions.
- **Wake Event:** A record of the user's actual wake time for a planning day, used as an input to Replan.
- **Replan:** The operation that creates a new Plan Version for the remaining planning day from updated user or schedule state while preserving protected commitments.
- **Occurrence:** One identifiable instance of a recurring Task series on a specific recurrence date, with identity that remains stable if its scheduled time moves.
- **Exception:** A per-Occurrence override that skips, moves, or modifies that Occurrence without changing the entire recurrence series.
- **Protected block:** A Plan Block that Replan and plan generation must not move because it represents a fixed commitment or a user-locked commitment; its plan-item state is `protected`.
- **Flexible task:** A Task whose Plan Block may be placed or moved by plan generation and Replan, subject to its constraints.
- **Fixed event:** A time-bound Event that is a hard scheduling constraint and is not moved by plan generation or Replan.
