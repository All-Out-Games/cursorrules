In your player
  public CameraControl CameraControl;

In your player Awake (with an IsLocal check). 
CameraControl = CameraControl.Create(0);
CameraControl.Zoom = 1.45f;

If you create a CameraControl, make sure to make it follow the player by setting its Position in the player's LateUpdate. 

Good values are between 0.8f and 2f the lower the closer the zoom.
