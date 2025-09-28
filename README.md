# Speech to Text and Foundation Models

This demo showcases how to use `ManipulationEvents` in visionOS 26 to link **gesture input** with **speech recognition** — turning an 8-Ball into a voice-activated experience powered by Apple’s Foundation model.

You can either tap the microphone manually to begin, **or** simply reach out and grab the 8-ball with your hand.

---

![Magic 8-Ball Demo](Simulator%20Screen%20Recording.gif)

---
### How It Works

- When the user **grabs the 3D 8-ball**, the system emits a `ManipulationEvents.WillBegin` event.
- As soon as that event fires, **speech recognition begins**, allowing the user to ask a question out loud.
- When the user **releases the ball**, `ManipulationEvents.WillEnd` fires.
- That event automatically stops the recording and sends the audio for processing.

These manipulation events provide **precise lifecycle hooks**, making it easy to create tactile, gesture-driven experiences without any custom gestures or buttons.

---

### Code Snippet

```swift
// Setup manipulation component and event listeners
@MainActor 
private func loadEightBallEntity(content: RealityViewContent) async {
    do {
        let entity = try await Entity(named: "EightBall", in: realityKitContentBundle)
        
        // Configure entity for manipulation
        ManipulationComponent.configureEntity(entity)
        
        // Add to scene before subscribing — required for event delivery
        content.add(entity)
        
        // Subscribe to manipulation lifecycle events
        willBeginSubscription = content.subscribe(
            to: ManipulationEvents.WillBegin.self,
            on: entity
        ) { _ in
            // Start speech recognition when user grabs the 8-ball
            speechManager.startRecording()
        }

        willEndSubscription = content.subscribe(
            to: ManipulationEvents.WillEnd.self,
            on: entity
        ) { _ in
            // Stop recording when user releases the 8-ball
            speechManager.stopRecording()
        }
        
    } catch {
        print("Failed to load entity: \(error)")
    }
}
