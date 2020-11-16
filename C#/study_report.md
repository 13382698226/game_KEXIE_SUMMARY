>**坑**
>
>1.fixupdate 会让跳跃的手感非常差  应该改为update
>
>2.自定义函数再在update中调用可以降低系统的内存
>
>3.动画之间用布尔型过渡的比较多
>
>4.把几个图片直接拖入unity可以直接生成animation
>
>5.角色的动画可以把他们的关节拆开再自己录动画

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class PlayerController : MonoBehaviour
{
    [SerializeField]private Rigidbody2D rb;
    public float speed;
    [SerializeField]private Animator anim;
    public Collider2D coll;
    public float jumpforce;
    public LayerMask ground;
    public int cherry;

    public Text CherryNum;
    private bool isHurt;//默认是false
    // Start is called before the first frame update
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        anim = GetComponent<Animator>();
    }

    // Update is called once per frame
    void FixedUpdate()
    {
        if (!isHurt)
        {
            Movement();
        }
            SwitchAnim();
    }
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.UpArrow)&& coll.IsTouchingLayers(ground))
        {
            rb.velocity = new Vector2(rb.velocity.x, jumpforce);
            anim.SetBool("jumping", true);

        }
    }
    void Movement()
    {
        float horizontalmove = Input.GetAxis("Horizontal");
        float facedirection = Input.GetAxisRaw("Horizontal");
        
        if (horizontalmove!=0)
        {
            rb.velocity = new Vector2(horizontalmove * speed*Time.deltaTime, rb.velocity.y);
            anim.SetFloat("running", Mathf.Abs(facedirection));
        }
        if(facedirection!=0)
        {
            transform.localScale = new Vector3(facedirection, 1, 1);
        }
        
    }
    void SwitchAnim()
    {
        anim.SetBool("idle", false);
        if (anim.GetBool("jumping"))
        {
            if(rb.velocity.y<0)
            {
                anim.SetBool("jumping", false);
                anim.SetBool("falling", true);
                
            }
        }else if (isHurt)
        {
            anim.SetBool("hurt", true);
            anim.SetFloat("running", 0);
            if(Mathf.Abs(rb.velocity.x)<0.1f)
            {
                anim.SetBool("hurt", false);
                anim.SetBool("idle", true);
                isHurt = false;
            }
        }
        else if (coll.IsTouchingLayers(ground))
        {
            Debug.Log(coll.IsTouchingLayers(ground));
            anim.SetBool("falling", false);
            anim.SetBool("idle", true);
        }
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.tag=="Collection")
        {
            Destroy(collision.gameObject);
            cherry +=1;
            CherryNum.text = cherry.ToString();
        }
    }
    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "enemy")
        {
            if (anim.GetBool("falling"))
            {
                Destroy(collision.gameObject);
                rb.velocity = new Vector2(rb.velocity.x, jumpforce);
                anim.SetBool("jumping", true);
            }else if(transform.position.x<collision.gameObject.transform.position.x)
            {
                rb.velocity = new Vector2(-5f, rb.velocity.y);
                isHurt = true;
            }else if (transform.position.x > collision.gameObject.transform.position.x)
            {
                rb.velocity = new Vector2(5f, rb.velocity.y);
                isHurt = true;
            }
        }
    }
}

```



---------

--------------

>***二段跳***
>
>设置一个空的gameobject，放在人物的脚底下，给瓦片地图设置碰撞体，自定义一个标签为ground，如果碰撞体的tag为ground来检测是否在地面上
>
>之后设置跳跃的次数，执行跳跃的命令后通过加减计数

```C#
if(isGrounded==true)
        {
            extraJumps = extraJumpsValue;
        }
        if(Input.GetKeyDown(KeyCode.UpArrow)&&extraJumps>0)
        {
            rb.velocity = Vector2.up * jumpForce;
            extraJumps--;
        }else if(Input .GetKeyDown(KeyCode.UpArrow )&&extraJumps==0&&isGrounded==true)
        {
            rb.velocity = Vector2.up * jumpForce; 
        }
    }
```

-----

------

>改变方向  直接用facingRight =! facingRight;

```C#
void Flip()
    {
        facingRight =! facingRight;
        Vector3 Scaler = transform.localScale;
        Scaler.x *= -1; 
        transform.localScale = Scaler;
    }
```

-------

------

>检测bug
>
>   Debug.Log();贞德有用