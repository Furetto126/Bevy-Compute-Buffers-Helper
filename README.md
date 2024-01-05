# Bevy-Compute-Buffers-Helper
A simple helper crate for managing buffers for a compute shader

# Usage

```rust
use bevy::{
    prelude::*,
    window::WindowPlugin, render::{RenderApp, Render},
};

use bevy_compute_buffers_helper::buffers_interface::*;

fn main() {
    let mut app = App::new();
    app.insert_resource(ClearColor(Color::BLACK))
        .add_plugins((
            DefaultPlugins.set(WindowPlugin {
                primary_window: Some(Window {
                    // uncomment for unthrottled FPS
                    // present_mode: bevy::window::PresentMode::AutoNoVsync,
                    ..default()
                }),
                ..default()
            }), 
            ComputeBuffersPlugin
        ));
    let render_app = app.sub_app_mut(RenderApp);
    render_app.add_event::<ComputeBufferUpdateEvent>()
        .add_systems(Render, modify_buffers);
    init_buffers(&mut render_app.world);

    app.run();
}

fn modify_buffers(
    commands: Commands,
    mut compute_buffers: ResMut<ComputeBuffers>
) {
    compute_buffers.set_value_at(1, vec![1.0 as f32, 0.0, 1.0], commands);
}

fn init_buffers(world: &mut World) {
    world.insert_resource(ShaderPath("shaders/shader.wgsl"));

    let compute_buffers = 
        ComputeBuffers::new(vec![
            ComputeBuffer::new(1, vec![0.0 as f32, 1.0, 0.5])
        ], world);

    world.insert_resource(compute_buffers);
}
```
