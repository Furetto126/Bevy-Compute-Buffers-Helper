# Bevy-Compute-Buffers-Helper
A simple helper crate for managing buffers for a compute shader

# Usage

```rust
use bevy::prelude::*;
use bevy::render::{RenderApp, Render};

use crate::buffers_interface::ComputeBuffersPlugin;

#[test]
fn test() {
    let mut app = App::new();
    app.add_plugins((DefaultPlugins, ComputeBuffersPlugin));

    app.sub_app_mut(RenderApp)
        // By initializing the buffers here in build() you make sure they exist before
        // starting to use them in any way
        .init_resource::<ComputeBuffers>()
        .add_event::<ComputeBufferUpdateEvent>()
        // Add systems that modify the buffers here
        // ----------------------------------------
        .add_systems(Render, modify_buffers);

    app.run();
}

fn modify_buffers(
    mut commands: Commands,
    mut compute_buffers: ResMut<ComputeBuffers>
) {
    compute_buffers.set_value_at(1, vec![100.0, 69.0], commands);
}

impl FromWorld for ComputeBuffers {
    // Basically an init function for the ComputeBuffers Resource
    fn from_world(world: &mut World) -> Self {
        world.insert_resource(ShaderPath("shaders/raytracer.wgsl"));
        ComputeBuffers::new(vec![
            ComputeBuffer::new(1, vec![0.0, 1.0, 2.0, 3.0])
        ], world)
    }
}
```
