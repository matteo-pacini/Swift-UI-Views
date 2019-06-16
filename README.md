# Swift-UI-Views

A collection of custom views to reuse in SwiftUI projects

![swiftui-logo](swiftui.png)

## Table of Contents

- [Switch](#switch) 
- [Activity Indicator](#activity-indicator)
- [Picker](#picker)

## Views

### Switch

Works on: `iOS`, `macOS`, `watchOS`.

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

Works on: `iOS`.

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

### Picker

Works on: `iOS`.

![picker-gif](picker.gif)

```swift
struct CustomPicker<Data>: UIViewRepresentable where Data: Equatable {

    @Binding var data: [[Data]]
    @Binding var selection: [Data]

    class Coordinator: NSObject, UIPickerViewDelegate, UIPickerViewDataSource {

        @Binding var data: [[Data]]
        @Binding var selection: [Data]

        init(data: Binding<[[Data]]>, selection: Binding<[Data]>) {
            $data = data
            $selection = selection
        }

        func pickerView(_ pickerView: UIPickerView,
                        numberOfRowsInComponent component: Int) -> Int {
            data[component].count
        }

        func numberOfComponents(in pickerView: UIPickerView) -> Int {
            data.count
        }

        func pickerView(_ pickerView: UIPickerView,
                        widthForComponent component: Int) -> CGFloat {
            return (pickerView.superview?.bounds.width ?? 0) * 0.33
        }

        func pickerView(_ pickerView: UIPickerView,
                        rowHeightForComponent component: Int) -> CGFloat {
            return 30
        }

        func pickerView(_ pickerView: UIPickerView,
                        viewForRow row: Int,
                        forComponent component: Int,
                        reusing view: UIView?) -> UIView {
            guard let reusableView = view as? UILabel else {
                let label = UILabel(frame: .zero)
                label.text = "\(data[component][row])"
                return label
            }
            reusableView.text = "\(data[component][row])"
            return reusableView
        }

        func pickerView(_ pickerView: UIPickerView,
                        didSelectRow row: Int,
                        inComponent component: Int) {
            let value = data[component][row]
            selection[component] = value
        }
    }

    func makeCoordinator() -> CustomPicker.Coordinator {
        return Coordinator(data: $data,
                           selection: $selection)
    }

    func makeUIView(context: UIViewRepresentableContext<CustomPicker>) -> UIPickerView {
        let picker = UIPickerView()
        picker.delegate = context.coordinator
        return picker
    }

    func updateUIView(_ uiView: UIPickerView,
                      context: UIViewRepresentableContext<CustomPicker>) {

        uiView.reloadAllComponents()
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.1) {
            self.selection.enumerated().forEach { tuple in
                let (offset, value) = tuple
                let row = self.data[offset].firstIndex { $0 == value } ?? 0
                uiView.selectRow(row, inComponent: offset, animated: false)
            }
        }

    }

}
```

**Demo:**

```swift
struct ContentView: View {

    @State var data: [[Int]] = [
        Array(0...10),
        Array(20...40),
        Array(100...200)
    ]
    @State var selection: [Int] = [0, 20, 100]

    var body: some View {
        NavigationView {
            VStack {
                CustomPicker(data: self.$data,
                             selection: self.$selection)
                Text("Selection: \(String(describing: selection))")
            }
        }
    }

}
```
