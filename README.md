# Swift-UI-Views

A collection of custom views to reuse in SwiftUI projects

![swiftui-logo](swiftui.png)

## Table of Contents

- [Switch](#switch)
- [Activity Indicator](#activity-indicator)

## Views

### Switch

 ![switch-gif](switch.gif)

```swift
struct CustomSwitch: View {

    @Binding var isOn: Bool

    var body: some View {
        ZStack {
            Capsule()
                .foregroundColor(isOn ? Color.green : Color.gray.opacity(0.35))
                .background(Color.clear)
                .frame(width: 100, height: 50)
            Circle()
                .foregroundColor(Color.white)
                .frame(width: 45, height: 45)
                .offset(x: isOn ? 25 : -25, y: 0)
                .shadow(color: .gray, radius: 3, x: 1, y: 1)
        }
        .tapAction {
            withAnimation {
                self.isOn.toggle()
            }
        }
    }

}
```

### Activity Indicator

![indicator-gif](indicator.gif)

```swift
struct ActivityIndicator: UIViewRepresentable {

    @Binding var isAnimating: Bool
    let style: UIActivityIndicatorView.Style

    func makeUIView(context: UIViewRepresentableContext<ActivityIndicator>) -> UIActivityIndicatorView {
        UIActivityIndicatorView(style: style)
    }

    func updateUIView(_ uiView: UIActivityIndicatorView,
                      context: UIViewRepresentableContext<ActivityIndicator>) {
        isAnimating ?
            uiView.startAnimating() :
            uiView.stopAnimating()
    }
}
```
