## Взлет и Полет к Муне

import krpc

conn = krpc.connect(name="Our mission")

sc = conn.space_center
mj = conn.mech_jeb
ascent = mj.ascent_autopilot

#All of these options will be filled directly into Ascent Guidance window and can be modified manually during flight.
ascent.desired_orbit_altitude = 100000
ascent.desired_inclination = 0

ascent.force_roll = True
ascent.vertical_roll = 90
ascent.turn_roll = 90
ascent.autostage = True
ascent.enabled = True

sc.active_vessel.control.activate_next_stage() #launch the vessel

with conn.stream(getattr, ascent, "enabled") as enabled:
    enabled.rate = 1 #we don't need a high throughput rate, 1 second is more than enough
    with enabled.condition:
        while enabled():
            enabled.wait()

print("Launch complete!")

Mun = conn.space_center.bodies["Mun"]
conn.space_center.target_body = Mun

print("Planning Hohmann transfer")
planner = mj.maneuver_planner
hohmann = planner.operation_transfer
hohmann.simple_transfer = True ### хз
print('hohmann makes nodes')
hohmann.make_nodes()
print('creates')

#check for warnings
warning = hohmann.error_message
if warning:
    print(warning)

def execute_nodes():
    print("Executing maneuver nodes")
    executor.execute_all_nodes()

    with conn.stream(getattr, executor, "enabled") as enabled:
        enabled.rate = 1  # we don't need a high throughput rate, 1 second is more than enough
        with enabled.condition:
            while enabled():
                enabled.wait()

# execute the nodes
executor = mj.node_executor
execute_nodes()

print("Rendezvous complete!")

sc.active_vessel.control.activate_next_stage()

conn.close()
