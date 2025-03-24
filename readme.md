# egui-Theme

## Theme selection & customization for egui

### Usage:

``` rust
use egui::Context;
use egui_theme::{Theme, ThemeKind, ThemeEditor, utils::widget_visuals};

struct MyApp {
    theme: Theme,
    // this is optional
    editor: ThemeEditor
}

impl MyApp {
    fn new(cc: &eframe::CreationContext<'_>) -> Self {
        // set the midnight theme
        let theme = Theme::new(ThemeKind::Midnight);
        // set the theme's style to egui
        cc.egui_ctx.set_style(theme.style.clone());
        Self { theme, editor: ThemeEditor::new() }
    }
}

impl eframe::App for MyApp {
    fn update(&mut self, ctx: &egui::Context, _frame: &mut eframe::Frame) {
        egui::CentralPanel::default().show(ctx, |ui| {

            // If you want to use the theme editor and see the changes in real time
            // apply any changes we made to the ui
            utils::apply_theme_changes(&self.theme, ui);
            // keep the editor open
            self.editor.open = true;
            // show the editor and get the new theme
            let new_theme = self.editor.show(&mut self.theme, ui);
            if let Some(theme) = new_theme {
                self.theme = theme;
            }

            let theme = &self.theme;
            let text = RichText::new("Hello world!").size(theme.text_sizes.normal);
            ui.add(text);

            let button_text = RichText::new("Click me!").size(theme.text_sizes.normal);
            if ui.add(Button::new(button_text)).clicked() {
                println!("Button clicked!");
            }

            ui.add(TextEdit::singleline(&mut self.text_edit_text).hint_text("Type something here..."));

            let frame = theme.frame1;
            // bg color for the uis inside the frame is different
            let bg_color = frame.fill;

            frame.show(ui, |ui| {
                ui.set_width(450.0);
                ui.set_height(250.0);
                
                // get the button visuals based on the bg color
                let visuals = theme.get_button_visuals(bg_color);
                let button_text = RichText::new("Click me!").size(theme.text_sizes.normal);
                widget_visuals(ui, visuals);
                if ui.add(Button::new(button_text)).clicked() {
                    println!("Button clicked!");
                }

                // get the text edit visuals based on the bg color
                let visuals = theme.get_text_edit_visuals(bg_color);
                widget_visuals(ui, visuals);
                ui.add(TextEdit::singleline(&mut self.text_edit_text).hint_text("Type something here..."));
            });

        });
    }
}
```

### Theme can also be serialized to be saved as a custom theme

``` rust
let theme_data = self.theme.to_json().unwrap();
let path = std::path::PathBuf::from("my-custom-theme.json");
std::fs::write(&save_path, data).unwrap();
```

### Load a custom theme

``` rust
let path = std::path::PathBuf::from("my-custom-theme.json");
let custom_theme = Theme::from_custom(path).unwrap();
```

#### You can also run the demo app to preview and customize themes

``` rust
cargo run --features demo --bin demo
```