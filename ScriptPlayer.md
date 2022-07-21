# PLAYER MOVIMENTACAO BASICA #

     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

    public class Player : MonoBehaviour
    {
        //variaveis do personagem
            public float speed;
            public float jumpForce;
    
            private Rigidbody2D rig;
            private Animator anim;
    
            private bool isJumping; 
            private bool doubleJumping; 

    // Start is called before the first frame update
    void Start()
    {
        rig = GetComponent<Rigidbody2D>();
        anim = GetComponent<Animator>(); 
    }

    // Update eh chamado a cada frame, logo tudo que estiver dentro dele sera trigado por frame
    void Update()
    {
        Move();

        Jump();
    }


    void Move()
    {
        //pegando a movimentacao conforme a tecla / botoes
        float movement = Input.GetAxis("Horizontal");

        //adicionando velocidade ao corpo do persona
        rig.velocity = new Vector2(movement * speed, rig.velocity.y);

        //tratamento para o personagem ir para a DIREITA
        if(movement > 0)
        {  //tratando bug do pulo, se pula nao anda  
           if(!isJumping){

            //executar animacao dele correndo
            anim.SetInteger("transition", 1);
           }
           
            //vector = vetores x,y,z  
            transform.eulerAngles = new Vector3(0,0,0);
        }
        //tratamento para o personagem ir para a ESQUERDA
        if(movement < 0 )
        {
           
           if(!isJumping)
           {
            anim.SetInteger("transition", 1);
           }
           
            transform.eulerAngles = new Vector3(0,180,0);
        }
        //se o personagem estiver parado executar a animacao dele parado E se nao estiver pulando
        if(movement == 0 && !isJumping)
        {
            anim.SetInteger("transition", 0);
        }
    
    }

    void Jump()
    {
        //lembre-se de colocar valor no "JumpForce" na parte do script
        //prescionando o botao do jump
       
        if(Input.GetButtonDown("Jump"))
        {   //checando se o isjump eh false ou nao
            //ou seja ele esta pulando? se for falso entao pula.
            if(!isJumping)
            {
            //animacao de pulo    
            anim.SetInteger("transition", 2);   
            //Adicionando uma forca no vetor(x,y)onde y = variavel de pulo, Forcemode = dar um impulso.
            //lembrando de verificar no RigidBody2D o "Gravity Scale" (escala de gravidade)
            rig.AddForce(new Vector2(0,jumpForce), ForceMode2D.Impulse); 
           
            //pra colocar um pulo duplo
            //quando aperto espaco (outra vez) meu doubleJumping = true e vai para o else
            doubleJumping = true;

            //pulo simples
            //quando aperto espaco meu isJumping = true
            isJumping = true;
            }
           else
           {
            //se o doubleJumping for verdadeiro de um outro pulo
            if(doubleJumping)
            {   
                //animacao de pulo
                anim.SetInteger("transition", 2); 
                //sendo verdadeiro de outro pulo dentro do primeiro pulo
                rig.AddForce(new Vector2(0,jumpForce), ForceMode2D.Impulse); 
                //e mude o doubleJumping para false para nao pular infinitamente
                doubleJumping = false;
            }
           }
        }

    
    }
    
    //Esse eh um colisor que toda vez que nosso objeto(perssonagem) enconta em outro colisor ele para
    void OnCollisionEnter2D(Collision2D coll)
    {   //verificando se realmente o colisor funciona se o personagem ta "tocando no chao".
        // se o personagem estiver tocando no chao (layer8) logo ele nao esta pulando(isjumping) = false
        if(coll.gameObject.layer == 8 )
        {   
            isJumping = false; 
        }

      }

     }
