using Systehttp://m.ComponentModel.Design;
using System.Reflection.Metadata.Ecma335;
using System.Runtime.Intrinsics.X86;
using System.Xml;
using static Program;

internal class Program
{
    private static void Main(string[] args)
    {
        Userinfo user1 = new Userinfo();
        CheckInsert userCheck1 = new CheckInsert();
        MainScreen mainScreen = new MainScreen();
        UserInfoScreen userscreen = new UserInfoScreen();
        InventoryScreen userInvenScreen = new InventoryScreen(user1); // 인벤토리 스크린에 유저 정보 연결

        bool insertCheck = true;
        do
        {
            user1.SelectJobPlz();
            string select = Console.ReadLine();

            if (userCheck1.CheckIntTrue(select))
            {
                if (userCheck1.IntCheck(select) >= 1 && userCheck1.IntCheck(select) <= 2)
                {
                    user1.Select(userCheck1.IntCheck(select));
                    user1.Jobseting();
                    userscreen.UpdateUserInfo(user1); // 유저 스탯창의 유저 정보 갱신
                    insertCheck = false;
                }
                else  // 1혹은 2외의 값을 입력받으면 다시 입력하라는 문구 출력
                {
                    Console.Clear();
                    Console.Write(" 1 혹은 2을 입력해 주세요!");
                    Console.ReadKey();
                }

            }
            else // 숫자 이외의 값을 입력 받으면 다시 입력하라는 문구 출력
            {
                Console.Clear();
                Console.Write("숫자를 입력해 주세요!");
                Console.ReadKey();

            }

        }
        while (insertCheck); // 유저의 직업을 정하는 코드




        mainScreen.DisplayScreen(); // 메인 스크린 출력

        insertCheck = true; // 게임을 종료 할지 말지 정하는 불 값 선언
        
        do // 게임이 종료되기전까지 입력 받은 값에따라 화면이 전환되는 반복문 수행
        {
            string select = Console.ReadLine();

            switch (select) // 입력받은 select의 값에따라 화면 출력
            {
                case "0":
                   mainScreen.DisplayScreen();
                    break;
                case "1":
                    userscreen.UpdateUserInfo(user1);
                    userscreen.DisplayUserInfo(user1);
                    break;

                case "2":
                    bool invenExit = true;
                    do
                    {
                        userInvenScreen.DisplayScreen();
                        bool insertCheck2 = true;
                        string select2 = Console.ReadLine();
                        if (select2 == "1")
                        {
                            do
                            {
                                userInvenScreen.DisplayScreen2();
                                string select3 = Console.ReadLine();
                                userInvenScreen.EqManager(select3);
                                if (select3 == "0")
                                {
                                    insertCheck2 = false;
                                }
                            }
                            while (insertCheck2);
                        }
                        else
                        {
                            invenExit = false;
                        }
                    }
                    while (invenExit);

                    break;
                case "3":
                    Console.WriteLine("프로그램이 종료됩니다.");
                    insertCheck = false;
                        break;

                default:
                    Console.WriteLine("올바른 선택이 아닙니다. 오류 메시지가 표시됩니다.");
                    break;
            }
        }
        while (insertCheck); // 화면 전환을 진행하는 코드

    }

    public class CheckInsert
    {

        int retrunInt = 0;
        bool checkInt = false;

        public int IntCheck(String needCheck)
        {
            if (int.TryParse(needCheck, out int retrunInt))
            {
                if (retrunInt >= 1 && retrunInt <= 2)
                {
                    //입력된 값 그대로 가야하므로 아무런 입력 X
                }
                else
                {
                    retrunInt = 0;
                }

            }
            else
            {
                retrunInt = 0;
            }

            return retrunInt;
        }

        public bool CheckIntTrue(String needCheck)
        {
            return int.TryParse(needCheck, out _) && int.Parse(needCheck) != 0;
        }

    } //입력받은 값이 정수인지 체크하는 클래스

    public class Userinfo // 플레이어의 기본 데이터!
    {
        public int lv = 1;
        public string[] chaDb = new string[3] {"","전사", "마법사"};
        public string SelectJob;
        public int atk = 10;
        public int def = 5;
        public int hp = 100;
        public int money = 1500;
        int select = 1;

        public void SelectJobPlz() //클래스를 선택하라는 안내문구 출력!
        {
            Console.Write("당신의 클래스를 정해주세요 [1: 전사, 2: 마법사] : ");
        }

        public void Jobseting() // 어떤 직업을 선택했는지 출력!
        {
            SelectJob = Select(select);
            Console.WriteLine("당신의 직업은 " + SelectJob + "입니다.");
            Console.ReadKey();
        }

        public String Select(int insert) // 어떤 직업을 선택했는지 픽업!
        {
            select = insert;
            if ( select < 0 && select >2 )
            {
                return chaDb[0];
            }
            return chaDb[select];
        }
    }



    public abstract class Screen // 화면의 틀이되는 추상클래스 
    {
        public void DisplayHeader()
        {
            Console.WriteLine("===================================================");
        }

        public abstract void DisplayContent();

        public void DisplayFooter()
        {
            Console.WriteLine("===================================================");
        }


    public void DisplayScreen() // 화면 출력
        {
            Console.Clear(); // 화면 초기화
            DisplayHeader(); // 헤더
            DisplayContent(); // 출력할 내용
            DisplayFooter(); // 바닥
        }
    }

    public class MainScreen : Screen
    {
        public override void DisplayContent()
        {
            Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다");
            Console.WriteLine("이곳에서 던전으로 들어가기 전 활동을 할 수 있습니다.\n");
            Console.WriteLine("1.상태 보기\n2.인벤토리 \n3.종료\n");
        }
    } // 메인화면을 출력하는 자식 클래스

    public class UserInfoScreen : Screen
    {

        public int lv;
        public string SelectJob;
        public int atk;
        public int def;
        public int hp;
        public int money;

        public void UpdateUserInfo(Userinfo user) // Userinfo클래스에서 정보를 가져오기위한 메서드
        {
            this.lv = user.lv;
            this.SelectJob = user.SelectJob;
            this.atk = user.atk;
            this.def = user.def;
            this.hp = user.hp;
            this.money = user.money;
        }
                public void DisplayUserInfo(Userinfo user) // 부모 클래스의 메서드 2개와 스택 출력 메서드를 호출
        {
            Console.Clear();
            DisplayHeader();
            DisplayContent();
            DisplayFooter();
        }
            public override void DisplayContent()  // 유저의 현재 스탯을 출력
        {
            Console.WriteLine("lv : " + lv);
            Console.WriteLine("직업 : " + SelectJob);
            Console.WriteLine("공격력 : " + atk);
            Console.WriteLine("방어력 : " + def);
            Console.WriteLine("생명력 : " + hp);
            Console.WriteLine("골드 : " + money);
            Console.WriteLine("\n0. 나가기, 3.종료");
        }
    } // 유저정보를 불러오는 클래스!

    public class InventoryScreen : Screen
    {
        private Userinfo user1; // 클래스 내에서 user1을 보관하기 위한 필드

        public InventoryScreen(Userinfo user1) // 메인에서 저장된 유저 정보를 연결할 메서드
        {
            this.user1 = user1;
        }


        bool echeck = false;
        bool echeck2 = false;
        int eq1 = 0;
        int eq2 = 0;
        public override void DisplayContent()
        {
            Console.WriteLine("[아이템 목록]\n무쇠갑옷 | 방어력 + 5 | 무쇠로 만들어져 튼튼한 갑옷입니다.{1}", eq1, eq1 == 1 ? "[E]" : "");
            Console.WriteLine(" 낡은 검 | 공격력 + 2 | 쉽게 볼 수 있는 낡은 검 입니다.{1}", eq2, eq2 == 1 ? "[E]" : "") ;
            Console.WriteLine("\n0.나가기\n1.장착관리");
        }

        public void DisplayContent2()
        {
            Console.WriteLine("[장착 관리]\n1. 무쇠갑옷 | 방어력 + 5 | 무쇠로 만들어져 튼튼한 갑옷입니다.{1}", eq1, eq1 == 1 ? "[E]" : "");
            Console.WriteLine("2. 낡은 검 | 공격력 + 2 | 쉽게 볼 수 있는 낡은 검 입니다.{1}", eq2, eq2 == 1 ? "[E]" : "");
            Console.WriteLine("\n0.나가기\n");
        }

        public void DisplayScreen2()
        {
            Console.Clear();
            DisplayHeader();
            DisplayContent2();
            DisplayFooter();
        }


        public void EqManager(string check) // 인벤토리의 장비 장착 여부에따라 플레이어의 공격력 방어력에 영향
        {
            switch (check)
            {
                case "0":
                    DisplayContent();
                    break;
                case "1":
                    if(echeck == false)
                    {
                        eq1 = 1;
                        echeck = true;
                        user1.def += 5;
                        break;
                    }
                    else
                    {
                        eq1 = 0;
                        echeck = false;
                        user1.def -= 5;
                        break;
                    }
                case "2":
                    {
                        if (echeck2 == false)
                        {
                            eq2 = 1;
                            echeck2 = true;
                            user1.atk += 5;
                            break;
                        }
                        else
                        {
                            eq2 = 0;
                            echeck2 = false;
                            user1.atk -= 5;
                            break;
                        }
                    }
                default:
                    {
                        Console.WriteLine("올바른 선택이 아닙니다. 오류 메시지가 표시됩니다.");
                        break;
                    }

                    }
        } // 무기의 장착과 해제를 담당하는 메서드 장착시 공격력과 방어력에 영향을준다!
    } //인벤토리의 기능을 담당하는 클래스!

}
