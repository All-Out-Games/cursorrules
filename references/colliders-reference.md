# Example of colliders (there are also Box_Colliders and Edge_Colliders)

public class RegionTrigger : Component
{
    public Polygon_Collider Collider;

    public override void Awake()
    {
        Collider = Entity.GetComponent<Polygon_Collider>();
        // So you can walk through it
        Collider.IsTrigger = true;
        Collider.OnCollisionEnter += other =>
        {
            var player = other.GetComponent<MyPlayer>();
            if (player.Alive())
            {
                player.RegionsVisited.Add(Entity.Name);
                if (Network.IsServer)
                {
                    player.Region.Set(Entity.Name);
                }
            }
        };
    }
}