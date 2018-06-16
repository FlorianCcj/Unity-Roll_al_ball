# Unity

I do eveerything > I do nothing

JS+html+css > Blender > Maya > Unity >= EDS amx > Real engine > Rpg maker

## Roll a ball

### Setting up the game

 * New project
 * 3D
 * Save scene
 * 3 menu in the right:
	* Hierarchy: component of the selected scene
	* Project: asset to help your project
	* Inspector: detail of the selection
 * if don't exist: 
	* Project -> create -> folder -> Scenes
	* LMB on the folder
	* Project -> create -> Scene -> MiniGame
 * Save
 * LMB on the scene
 * Hierarchy -> 3D Object -> Plane
 * Rename -> Ground
 * Inspector -> Transform -> Option -> reset
 * Inspector -> Transform -> Scale -> (x, y, z) -> (2, 1, 2)
 * Scene -> LMB on Ground -> F
 * Hierarchy -> 3D Object -> Sphere
 * Rename -> Player
 * Inspector -> Transform -> Option -> reset
 * Inspector -> Transform -> Position -> y -> .5
 * Project -> create -> folder -> Materials
 * Project -> create -> Material -> Rename -> Background
 * Inspector -> Main Map -> Albedo -> (R, G, B) -> (0, 32, 64)
 * Hierarchy -> LMB Directional Light -> Inspector -> Rotation -> Y -> 60
 
### Moving the player

 * Hierarchy -> Player -> Inspector -> Add Component -> Physics -> Rigidbody
 * Inspector -> Sphere Collider -> Option -> Move Down
 * Project -> create -> folder -> Scripts
 * Inspector -> Add Component -> New Script -> PlayerController
 * Project -> Scripts -> PlayerController -> Double click

```
// C#
// Scripts/PlayerController.cs
using UnityEngine;
using System.Collections;

public class PlayerController : MonoBehaviour {

    public float speed;

    private Rigidbody rb;

    void Start ()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate ()
    {
        float moveHorizontal = Input.GetAxis ("Horizontal");
        float moveVertical = Input.GetAxis ("Vertical");

        Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);

        rb.AddForce (movement * speed);
    }
}
```

 * Inspector -> Player Controller -> speed -> 10
 * Play

### Moving the camera

 * Hierarchy -> Main Camera -> Inspector -> Transform -> Position -> (X, Y, Z) -> (0, 10, -10)
 * Hierarchy -> Main Camera -> Inspector -> Transform -> -> Rotation -> (X, Y, Z) -> (45, 0, 0)
 * Hierarchy -> Main Camera -> Inspector -> Add Component -> New Script -> CameraController
 * Hierarchy -> Main Camera -> LMB -> click and drag -> Hierarchy -> Player -> To -> Inspector -> CameraController -> Player

```
// C#
// Scripts/CameraController.cs
using UnityEngine;
using System.Collections;

public class CameraController : MonoBehaviour {

    public GameObject player;

    private Vector3 offset;

    void Start ()
    {
        offset = transform.position - player.transform.position;
    }
    
	// after everyone
    void LateUpdate ()
    {
        transform.position = player.transform.position + offset;
    }
}
```

### Setting up the Play Area

 * Hierarchy -> create -> Create empty -> rename -> Walls -> inspector -> Transform -> option -> reset
 * Hierarchy -> create -> 3D Object -> Cube -> rename -> West Wall -> inspector -> Transform -> option -> reset
 * West Wall -> Click and drag -> Put on -> Wall
 * Inspector -> Transform -> Scale -> (X, Y, Z) -> (.5, 2, 20.5)
 * Inspector -> Transform -> Position -> (X, Y, Z) -> (-10, 0, 0)
 * Hierarchy -> West Wall -> RMB -> Duplicate -> Rename -> East Wall -> Inspector -> Position -> X -> 10
 * Hierarchy -> West Wall -> RMB -> Duplicate -> Rename -> North Wall -> Inspector -> Scale -> (X, Y, Z) -> (20.5, 2, .5) -> Position -> (X, Y, Z) -> (0, 0, 10)
 * Hierarchy -> North Wall -> RMB -> Duplicate -> Rename -> South Wall -> Inspector -> Position -> (X, Y, Z) -> (0, 0, -10)

### Creating Collectable Objects

 * Hierarchy -> create -> 3D Object -> Cube -> Rename -> PickUp -> inspector -> Transform -> option -> reset
	* -> transform -> Position -> Y -> .5
	* -> transform -> Rotation -> (X, Y, Z) -> (45, 45, 45)
	* -> transform -> Scale -> (X, Y, Z) -> (.5, .5, .5)
	* Add Component -> New Script -> Rotator
 * Project -> create -> folder -> Prefabs
 * Hierarchy -> PickUp -> Click and drag -> to -> Prefabs (generate a prefab which permit to re-create this component and change all in one)
 * Hierarchy -> create ->  Create empty -> rename -> PickUps -> inspector -> Transform -> option -> reset
 * Top Left -> Local -> Global (permit to change referential)
 * Project -> Prefabs -> PickUp -> Click and Drag -> Add 12 PickUp in PickUps and deals it in the maps
 * Project -> Material -> Background -> Ctrl + D -> Rename -> PickUp -> Albedo (R, G, B) -> (255, 255, 0)
	* -> Click and drag -> To -> Prefab -> PickUp

```
// C#
// Scripts/Rotator.cs
using UnityEngine;
using System.Collections;

public class Rotator : MonoBehaviour {

    void Update () 
    {
        transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
    }
}
```

### Collecting the Pick Up Objects 

```
// C#
// Scripts/PlayerController.cs

using UnityEngine;
using System.Collections;

public class PlayerController : MonoBehaviour {

    public float speed;

    private Rigidbody rb;

    void Start ()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate ()
    {
        float moveHorizontal = Input.GetAxis ("Horizontal");
        float moveVertical = Input.GetAxis ("Vertical");

        Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);

        rb.AddForce (movement * speed);
    }

    void OnTriggerEnter(Collider other) 
    {
        if (other.gameObject.CompareTag ("Pick Up"))
        {
            other.gameObject.SetActive (false);
        }
    }
}
```
 * Project -> Prefabs -> PickUp -> Box Collider -> Is Trigger -> enable it
 * Project -> Prefabs -> PickUp -> Header -> Tag -> Add Tag -> Pick Up -> then select it
This work for non moving object but to dynamic object it s not enougth
 * Project -> Prefabs -> PickUp -> Add Component -> Physics -> Rigidbody -> Is Kinematic -> enable it
 
 
### Displaying the Score and Text

 * Hierarchy -> Create -> UI -> Text -> Rename -> Count Text
 * Inspector -> Transform -> Anchors Preset -> Alt + Shift + LMB -> Top left
 * Inspector -> Transform -> (X, Y, Z) -> (10, -10, 0)
 * Inspector -> Text -> Count Text
 * Click And Drag -> From -> Hierarchy -> To -> Player -> Inspector -> PlayerController
 * Hierarchy -> Create -> UI -> Text -> Rename -> Win Text
 * Inspector -> Text -> Win Text -> Font Size -> 24
 * Inspector -> Transform -> Anchors Preset -> Alt + Shift + LMB -> Center Midle -> Pos Y -> 75
 

```
// C#
// Scripts/PlayerController.cs
using UnityEngine;
using UnityEngine.UI;
using System.Collections;

public class PlayerController : MonoBehaviour {

    public float speed;
    public Text countText;
    public Text winText;

    private Rigidbody rb;
    private int count;

    void Start ()
    {
        rb = GetComponent<Rigidbody>();
        count = 0;
        SetCountText ();
        winText.text = "";
    }

    void FixedUpdate ()
    {
        float moveHorizontal = Input.GetAxis ("Horizontal");
        float moveVertical = Input.GetAxis ("Vertical");

        Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);

        rb.AddForce (movement * speed);
    }

    void OnTriggerEnter(Collider other) 
    {
        if (other.gameObject.CompareTag ( "Pick Up"))
        {
            other.gameObject.SetActive (false);
            count = count + 1;
            SetCountText ();
        }
    }

    void SetCountText ()
    {
        countText.text = "Count: " + count.ToString ();
        if (count >= 12)
        {
            winText.text = "You Win!";
        }
    }
}
```

### Build



### Share it in git

 * Create a Repos in github it will ask you the language, choose Unity
 * Clone it
 * copy the project in it
 * add commit and push
 * in theory only Assets and Project Settings directory will be push