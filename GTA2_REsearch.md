---
layout: page
title: GTA2 Reverse Engineering notes...
permalink: /gta2re/
---
Grand Theft Auto 2 REsearch by V0alz
--------------------------------------------
[Research start	=	15:03 03/05/2016]
[Last update	=	17:30 06/05/2016]

STATIC VARIABLES:
=================
m_playerPositionX = 0x0066FEF8 [GTA2.EXE+27FEF8]
m_playerPositionY = 0x00663344 [GTA2.EXE+273344]
m_playerPositionZ = 0x0066FEA0 [GTA2.EXE+27FEA0]
byte_multiplayer =  0x00673E2C []
=================

Potential funcs:
=================
Player::AddScore( ukn, int value ) = 0044B2C0
Player??IsDead( unk, char ) = 004A4D50
=================

=========================
Structs.
-------------------------
NOTE: I doubt any of
this is 100% right...
=========================
PlayerInfo:
0000 - Paused?           2763018 : 2                   
0004 - player1ptr        276301C : P->09748198         
0008 - player2ptr        2763020 : P->00000000         
000C - player3ptr        2763024 : P->00000000         
0010 - player4ptr        2763028 : P->00000000         
0014 - player5ptr        276302C : P->00000000         
0018 - player6ptr        2763030 : P->00000000         
001C - myPlayerPtr       2763034 : P->09748198         
0020 - 2 Bytes           2763038 : 257                 
0022 - Byte              276303A : 0                   
0023 - numOfPlayers      276303B : 1                   
0024 - myPlayerID        276303C : 0                   
0025 - Byte              276303D : 0                   
0026 - Byte              276303E : 0                   
0027 - 2 Bytes           276303F : 65280               
0029 - Byte              2763041 : 255                 
002A - Byte              2763042 : 255                 
002B - Byte              2763043 : 255                 
002C - 4 Bytes           2763044 : 0                   
0030 - 4 Bytes           2763048 : 0                   
0034 - 4 Bytes           276304C : 0                   
0038 - myPlayerPtr       2763050 : P->09748198    
=========================
=========================

=======================================================================
HACKY HACKY HACKY HACKY HACKY HACKY HACKY HACKY
ALL C/C++
=======================================================================
// When the game is playing(not in main menu)
// this function fetches the player info and stores it into the
// struct(PlayerInfo)
bool Core::GetPlayerInfo()
{
	DWORD* ptrToPlayerInfo = (DWORD*)0x5eb4fc;
	if( *ptrToPlayerInfo == 0 )
	{
		return false;
	}
	else
	{
		memcpy( &m_playerInfo, (PVOID)*ptrToPlayerInfo, sizeof( PlayerInfo ) );
		return true;
	}
}

======================================================================
ALL UNORGANIZED NOTES
IDK ABOUT FORMAT BEYOND HERE SO MAY BE HARD TO READ!
======================================================================

Stuff known about lives:
------------------------
Lives is in ECX at 0044B2EC
ECX gets set at 004A4D65 - add ecx, 684
	This suggests that ECX whilst at 004A4D61 is ptr to Player Ped. -- CORRECT!!
LifesPtr - 684 = PlayerPtr
During IsDead EBP containers pointer to Player.ptr
	FIND OUT WHAT SETS EBP READY FOR THIS SECTION OF CODE.

Instruction to remove life when wasted is at 0044B2EC
To remove life reduction fill 0044B2EA with 4 NOPs

end lives
-------------------------

0x5EB4FC = ptr to player info
this+4 = id? or which player am i
this+1C = player ptr

Money is in ECX at 0044B2D6

Found interesting thing looking at m_playerPositionX:
	004B99AA - 89 88 18FE6600  - mov [eax+0066FE18],ecx
