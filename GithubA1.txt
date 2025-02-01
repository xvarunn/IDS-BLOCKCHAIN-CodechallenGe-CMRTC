from datetime import datetime, timedelta

class Event:
    def _init_(self, name, start_time, end_time):
        self.name = name
        self.start_time = datetime.strptime(start_time, "%H:%M")
        self.end_time = datetime.strptime(end_time, "%H:%M")

    def _repr_(self):
        return f'"{self.name}", Start: "{self.start_time.strftime("%H:%M")}", End: "{self.end_time.strftime("%H:%M")}"'

def parse_events(events):
    return [Event(name, start, end) for name, start, end in events]

def sort_events(events):
    return sorted(events, key=lambda x: x.start_time)

def detect_conflicts(events):
    conflicts = []
    for i in range(len(events) - 1):
        if events[i].end_time > events[i+1].start_time:
            conflicts.append((events[i], events[i+1]))
    return conflicts

def suggest_resolutions(conflicts, events):
    suggestions = []
    for event1, event2 in conflicts:
        # Find the next available slot after the last event
        last_event_end = max([e.end_time for e in events])
        new_start = last_event_end
        new_end = new_start + (event2.end_time - event2.start_time)
        suggestions.append((event2, new_start, new_end))
    return suggestions

def main():
    events_data = [
        ("Meeting A", "09:00", "10:30"),
        ("Workshop B", "10:00", "11:30"),
        ("Lunch Break", "12:00", "13:00"),
        ("Presentation C", "10:30", "12:00")
    ]

    events = parse_events(events_data)
    sorted_events = sort_events(events)
    conflicts = detect_conflicts(sorted_events)
    resolutions = suggest_resolutions(conflicts, sorted_events)

    print("Sorted Schedule:")
    for event in sorted_events:
        print(event)

    print("\nConflicting Events:")
    for event1, event2 in conflicts:
        print(f'1. {event1.name} and {event2.name}')

    print("\nSuggested Resolutions:")
    for event, new_start, new_end in resolutions:
        print(f'Reschedule "{event.name}" to Start: "{new_start.strftime("%H:%M")}", End: "{new_end.strftime("%H:%M")}"')

if _name_ == "_main_":
    main()