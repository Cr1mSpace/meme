anchor init memecoin
cd memecoin

use anchor_lang::prelude::*;
use anchor_spl::token::{self, Mint, TokenAccount, Token};

declare_id!("TwójProgramID");

#[program]
pub mod memecoin {
    use super::*;
    pub fn create_token(ctx: Context<CreateToken>, amount: u64) -> Result<()> {
        let cpi_accounts = token::MintTo {
            mint: ctx.accounts.mint.to_account_info(),
            to: ctx.accounts.token_account.to_account_info(),
            authority: ctx.accounts.authority.to_account_info(),
        };
        let cpi_program = ctx.accounts.token_program.to_account_info();
        let cpi_ctx = CpiContext::new(cpi_program, cpi_accounts);
        token::mint_to(cpi_ctx, amount)?;
        Ok(())
    }
}

#[derive(Accounts)]
pub struct CreateToken<'info> {
    #[account(mut)]
    pub mint: Account<'info, Mint>,
    #[account(mut)]
    pub token_account: Account<'info, TokenAccount>,
    #[account(signer)]
    pub authority: AccountInfo<'info>,
    pub token_program: Program<'info, Token>,
}
