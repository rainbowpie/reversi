//****************************************** 
// 黑白棋單機對戰版(無ai) 
// 2006年1月20日下午2:12完成 
// by yag 
//****************************************** 
#include <iostream> 
#include <string> 

using namespace std; 

short Board[8][8] = { 0, 0, 0, 0, 0, 0, 0, 0,   // 0代表空格，1代表黑棋(先動)，2代表白棋 
                 0, 0, 0, 0, 0, 0, 0, 0, 
                 0, 0, 0, 0, 0, 0, 0, 0, 
                 0, 0, 0, 2, 1, 0, 0, 0, 
                 0, 0, 0, 1, 2, 0, 0, 0, 
                 0, 0, 0, 0, 0, 0, 0, 0, 
                 0, 0, 0, 0, 0, 0, 0, 0, 
                 0, 0, 0, 0, 0, 0, 0, 0 }; 

// 顯示棋盤 
void ShowBoard(); 

// 輸入檢查，輸入可以是3d、d3、3D、D3等4種格式 
// 回傳0代表輸入可接受，回傳3代表輸入格式錯誤 
short InputAnalyze( string strInput, short *spX, short *spY ); 

// 檢查(sX,sY)這格是否可以放子 
// 如果bJustCheck是true，那麼不會對棋盤進行更動，如果是false，則代表(sX,sY)確實已落子，對棋盤做出相應的變化 
// 回傳3代表(sX,sY)不能放子，否則回傳下一手輪到誰(1為黑，2為白) 
short MoveAnalyze( short sTurn, short sX, short sY, bool bJustCheck ); 

// 檢查是否已下完或者需要Pass 
// 如果遊戲已結束，回傳0 
// 如果需要Pass，回傳下一手輪到誰(1為黑，2為白) 
short WinPassCheck( short sMF ); 

int main() 
{ 
   string strInput = ""; 
   short sMF = 1, sWF = 0;   // MoveFlag WrongFlag 
   short sMPX = 0, sMPY = 0;   // MovePoint 

   while( 1 ) 
   { 
      ShowBoard(); 
      sMF = WinPassCheck( sMF ); 
    
      if( sMF == 1 ) 
         cout << "Black:>"; 
      else if( sMF == 2 ) 
         cout << "White:>"; 
      else if( sMF == 0 ) 
         break; 

      cin >> strInput; 

      sWF = InputAnalyze( strInput, &sMPX, &sMPY ); 

      if( sWF == 0 ) 
         sWF = MoveAnalyze( sMF, sMPX, sMPY, false ); 

      if( sWF == 3 ) 
         system( "pause" ); 
      else 
         sMF = sWF; 
       
      system( "cls" ); 
      strInput = ""; 
   } 

   return 0; 
} 

void ShowBoard() 
{ 
   int x=0, y=0; 

   cout << " ａｂｃｄｅｆｇｈ" << endl; 

   for( y = 0; y < 8; y++ ) 
   { 
      cout << 8-y; 

      for( x = 0; x < 8; x++ ) 
      { 
         if( Board[y][x] == 0 ) 
            cout << "□"; 
         else if( Board[y][x] == 1 ) 
            cout << "○";   // 因為在DOS下會用補色來顯示，所以○會變黑棋 
         else if( Board[y][x] == 2 ) 
            cout << "●";   // 同理，●是白棋 
      } 

      cout << endl; 
   } 
} 

short InputAnalyze( string strInput, short *spX, short *spY ) 
{ 
   if( strInput.length() != 2 )   // 檢查輸入是否為2碼 
   { 
      cout << "請輸入2碼之棋盤座標" << endl; 
      return 3; 
   } 

   if( isdigit( strInput[0] ) )   // 檢查第一個字元是否數字 
   { 
      *spY = 7-(short)( strInput[0] - 49 );   // 將數字字元轉成數字('1' = 0, '2' = 1 ... ) 
      if( *spY < 0 || *spY > 7 )   // 檢查是否在棋盤範圍內 
      { 
         cout << "錯誤的輸入，請重新嘗試" << endl; 
         return 3; 
      } 

      if( isalpha( strInput[1] ) )   // 檢查第二個字元是否英文字 
      { 
         if( islower( strInput[1] ) ) 
            *spX = (short)( strInput[1] - 97 );      // 將英文小寫轉成數字 
                                          // ('a' = 0, 'b' = 1 ... ) 
         else if( isupper( strInput[1] ) ) 
            *spX = (short)( strInput[1] - 65 );      // 將英文大寫轉成數字 

         if( *spX < 0 || *spX > 7 ) 
         { 
            cout << "錯誤的輸入，請重新嘗試" << endl; 
            return 3; 
         }    
      } 
      else 
      { 
         cout << "輸入應為1個數字1個字母" << endl;   // 如果第一個是數字而第二個不是英文字，代表輸入有誤 
         return 3; 
      } 
   } 
   else if( isalpha( strInput[0] ) )   // 如果第一個不是數字，檢查是否英文字 
   { 
      if( islower( strInput[0] ) ) 
         *spX = (short)( strInput[0] - 97 ); 
      else if( isupper( strInput[0] ) ) 
         *spX = (short)( strInput[0] - 65 ); 

      if( *spX < 0 || *spX > 7 ) 
      { 
         cout << "錯誤的輸入，請重新嘗試" << endl; 
         return 3; 
      } 

      if( isdigit( strInput[1] ) ) 
      { 
         *spY = 7-(short)( strInput[1] - 49 ); 
         if( *spY < 0 || *spY > 7 ) 
         { 
            cout << "錯誤的輸入，請重新嘗試" << endl; 
            return 3; 
         } 
      } 
      else 
      { 
         cout << "輸入應為1個數字1個字母" << endl;   // 如果第一個是英文字卻不是數字，代表輸入有誤 
         return 3; 
      } 
   } 
   else 
   { 
      cout << "輸入了非數字或字母的字元" << endl;   // 第一個不是英文字也不是數字，輸入有誤 
      return 3; 
   } 

   return 0; 
} 

short MoveAnalyze( short sTurn, short sX, short sY, bool bJustCheck ) 
{ 
   if( Board[sY][sX] != 0 ) 
   { 
      cout << "此格已有棋子" << endl; 
      return 3; 
   } 

   short sEnemy = 0; 
   bool bIsChanged = false; 

   if( sTurn == 1 ) 
      sEnemy = 2; 
   else 
      sEnemy = 1; 

   short i = 1, p = 0; 

   if( sX + 1 < 8 && Board[sY][sX + 1] == sEnemy )      // 檢查落子點右邊是否是對手的棋子 
   { 
      while( sX + i < 8 && Board[sY][sX + i] == sEnemy )   // 是的話一直往右側看過去直到不是 
         i++; 
      if( sX + i < 8 && Board[sY][sX + i] == sTurn )   // 確認另一端是否是我們的子 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY][sX + p] = sTurn;    
// 如果是我們的子且非為純檢查模式，將這條線上的子都翻成我方 

         bIsChanged = true; 
      } 
   } 
   if( sX - 1 >= 0 && Board[sY][sX - 1] == sEnemy )   // 檢查左邊 
   { 
      i = 1; 

      while( sX - i >= 0 && Board[sY][sX - i] == sEnemy ) 
         i++; 
      if( sX - i >= 0 && Board[sY][sX - i] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY][sX - p] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sY + 1 < 8 && Board[sY + 1][sX] == sEnemy )      // 下面 
   { 
      i = 1; 

      while( sY + i < 8 && Board[sY + i][sX] == sEnemy ) 
         i++; 
      if( sY + i < 8 && Board[sY + i][sX] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY + p][sX] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sY - 1 >= 0 && Board[sY - 1][sX] == sEnemy )   // 上面 
   { 
      i = 1; 

      while( sY - i >= 0 && Board[sY - i][sX] == sEnemy ) 
         i++; 
      if( sY - i >= 0 && Board[sY - i][sX] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY - p][sX] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sX + 1 < 8 && sY + 1 < 8 && Board[sY + 1][sX + 1] == sEnemy )      // 右下 
   { 
      i = 1; 

      while( sX + i < 8 && sY + i < 8 && Board[sY + i][sX + i] == sEnemy ) 
         i++; 
      if( sX + i < 8 && sY + i < 8 && Board[sY + i][sX + i] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY + p][sX + p] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sX - 1 >= 0 && sY + 1 < 8 && Board[sY + 1][sX - 1] == sEnemy )      // 左下 
   { 
      i = 1; 

      while( sX - i >= 0 && sY + i < 8 && Board[sY + i][sX - i] == sEnemy ) 
         i++; 
      if( sX - i >= 0 && sY + i < 8 && Board[sY + i][sX - i] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY + p][sX - p] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sX - 1 >= 0 && sY - 1 >= 0 && Board[sY - 1][sX - 1] == sEnemy )      // 左上 
   { 
      i = 1; 

      while( sX - i >= 0 && sY - i >= 0 && Board[sY - i][sX - i] == sEnemy ) 
         i++; 
      if( sX - i >= 0 && sY - i >= 0 && Board[sY - i][sX - i] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY - p][sX - p] = sTurn; 

         bIsChanged = true; 
      } 
   } 
   if( sX + 1 < 8 && sY - 1 >= 0 && Board[sY - 1][sX + 1] == sEnemy )      // 右上 
   { 
      i = 1; 

      while( sX + i < 8 && sY - i >= 0 && Board[sY - i][sX + i] == sEnemy ) 
         i++; 
      if( sX + i < 8 && sY - i >= 0 && Board[sY - i][sX + i] == sTurn ) 
      { 
         if( !bJustCheck ) 
            for( p = 0; p < i; p++ ) 
               Board[sY - p][sX + p] = sTurn; 

         bIsChanged = true; 
      } 
   } 
       
   if( bIsChanged )   // 如果落子可造成改變，回傳下一手換誰 
      return sEnemy; 
   else 
   { 
      if( !bJustCheck ) 
         cout << "這格無法造成任何攻擊，不能放置" << endl;    
// 如果不能造成改變且非為純檢查模式，輸出警告訊息 
      return 3; 
   } 
} 

short WinPassCheck( short sMF ) 
{ 
   short x = 0, y = 0; 
   bool bIsEnd = true; 
   short sBlackCount = 0, sWhiteCount = 0; 

   for( y = 0; y < 8; y++ ) 
   { 
      for( x = 0; x < 8; x++ ) 
      { 
         if( Board[y][x] == 0 ) 
         { 
            bIsEnd = false;   // 還有空格，假設遊戲未結束 
            if( MoveAnalyze( sMF, x, y, true ) != 3 )    
// 用純檢查模式查看每個未放子的點是否有辦法放子 
               return sMF;   // 只要找到一個可以放子的地方，代表不會已結束或必須Pass，直接回傳 
         } 
         else if( Board[y][x] == 1 ) 
            sBlackCount++; 
         else if( Board[y][x] == 2 ) 
            sWhiteCount++; 
      } 
   } 

   if( sWhiteCount == 0 || sBlackCount == 0 )   // 有一方被吃光了，即使還有空格，遊戲也提早結束 
      bIsEnd = true; 

   if( bIsEnd == false )   // 如果沒地方可以放子，但遊戲未結束，代表必須Pass 
   { 
      cout << "這輪你無法造成任何攻擊，只能Pass" << endl; 
      system( "pause" ); 
      system( "cls" ); 
      ShowBoard(); 
      return ( sMF == 1 ? 2 : 1 );   // sMF為1回傳2，不為1回傳1 
   } 
   else   // 遊戲結束 
   { 
      if( sBlackCount > sWhiteCount ) 
         cout << "黑方以" << sBlackCount << "比" << sWhiteCount << "贏得勝利！" << endl; 
      else if( sWhiteCount > sBlackCount ) 
         cout << "白方以" << sWhiteCount << "比" << sBlackCount << "贏得勝利！" << endl; 
      else 
         cout << "雙方以" << sWhiteCount << "比" << sBlackCount << "平手！" << endl; 

      system( "pause" ); 
      system( "cls" ); 
      cout << "遊戲結束。" << endl; 
      system( "pause" ); 
      return 0; 
   } 
}
