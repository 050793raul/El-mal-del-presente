using UnityEngine;

public enum PlayerType
{
    Scientist,
    Soldier,
    MexicanSoldier,
    Convict
}

public class PlayerController : MonoBehaviour
{
    public PlayerType playerType;
    public float health = 100f;
    public float speed = 6f;
    public float jumpForce = 8f;

    protected CharacterController controller;
    protected Vector3 moveDirection = Vector3.zero;
    protected float gravity = 20.0f;

    protected virtual void Start()
    {
        controller = GetComponent<CharacterController>();
        SetStatsByType();
    }

    protected virtual void Update()
    {
        HandleMovement();
    }

    protected void HandleMovement()
    {
        if (controller.isGrounded)
        {
            float moveX = Input.GetAxis("Horizontal");
            float moveZ = Input.GetAxis("Vertical");
            Vector3 move = transform.right * moveX + transform.forward * moveZ;
            moveDirection = move * speed;

            if (Input.GetButtonDown("Jump"))
                moveDirection.y = jumpForce;
        }
        moveDirection.y -= gravity * Time.deltaTime;
        controller.Move(moveDirection * Time.deltaTime);
    }

    protected virtual void SetStatsByType()
    {
        switch (playerType)
        {
            case PlayerType.Scientist:
                health = 70;
                speed = 5;
                break;
            case PlayerType.Soldier:
                health = 100;
                speed = 6;
                break;
            case PlayerType.MexicanSoldier:
                health = 110;
                speed = 7;
                break;
            case PlayerType.Convict:
                health = 90;
                speed = 6.5f;
                break;
        }
    }
}

using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using UnityEngine.UI;

public class RandomJokeGenerator : MonoBehaviour
{
    // Assign this in the Unity Inspector to a UI Text component to display the joke
    public Text jokeText;

    // Example external joke API (Official Joke API)
    private string apiUrl = "https://official-joke-api.appspot.com/random_joke";

    // Call this method to fetch a joke (e.g., from a button OnClick)
    public void GetRandomJoke()
    {
        StartCoroutine(FetchJokeCoroutine());
    }

    private IEnumerator FetchJokeCoroutine()
    {
        UnityWebRequest request = UnityWebRequest.Get(apiUrl);
        yield return request.SendWebRequest();

        if (request.result == UnityWebRequest.Result.Success)
        {
            // Parse the joke from the JSON response
            JokeResponse joke = JsonUtility.FromJson<JokeResponse>(request.downloadHandler.text);
            jokeText.text = $"{joke.setup}\n{joke.punchline}";
        }
        else
        {
            jokeText.text = "Failed to load joke. Please try again!";
        }
    }

    // Match the JSON structure from the API
    [System.Serializable]
    private class JokeResponse
    {
        public string setup;
        public string punchline;
    }
}
