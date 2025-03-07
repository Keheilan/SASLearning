/*******************************************************/
/*     SAS Example of  
/*     Proc Tabulate and Proc Report
/*     Using generated population data
/*     to showcase the use of nested tables, custom formats
/*     and custom calculations
/*     Created by: Saida Harle Provincial Health Services
/*     Date: March 3rd
/*     Purpose: Tips and Tricks working with data
/*******************************************************/

/* Generate data */

data pop_estimates_with_rate;
 /* random seed for reproducibility */
   call streaminit(123);

   /* random seed for reproducibility */
   length age_grp $10 sex $10;
   do Region = 'Eastern', 'Western';
      do HSDA = 'Parkdale', 'SageHill';
         do yr_cal = '2025', '2030', '2035';
            total_population = 0;

            /* initiate total pop for each year and region */
            do age_grp = 'Y049', 'Y5079', 'Y80+';
               do sex = 'Male', 'Female';
                  do disease_type = 'stage1', 'stage2';
                     pop_est = abs(round(rand('gaussian') * 100 + 101));
                     /* generate pop estimates */
                     total_population = pop_est;
                     rate_disease = round(rand('uniform') * 50);
                     weight_rate = 0.2;
                     output;
                  end;
               end;
            end;
         end;
      end;
   end;
run;

							
proc format;
   value ratefmt
      low - <50 = ' #6ba339'
      51 - <150 = '#8e3e9e'
      151 - <250 = '#6dbb70'
      other = '#cc6565';
run;

proc tabulate data=pop_estimates_with_rate;
   title1 "Summary of generated data for years 2025 by Region and HSDA";
   class region hsda yr_cal sex disease_type age_grp;
   var pop_est rate_disease;
   table region='*' (hsda='all Total Region')*disease_type='',
         (yr_cal='Year')*(sex='*' all='Total Year' Sex Total)*age_grp*
         (pop_est='*' *sum='Pop Estimate' rate_disease='*' *sum='Crude rate')
         / style={background=ratefmt.};

   borderwidth=2 cellspacing=5 box="Region | HSDA | Disease Type" indent=3;
   where yr_cal='2025';
run;
							
proc report data=pop_estimates_with_rate out=blah nowd;
   column Region HSDA disease_type (yr_cal age_grp sex (pop_est rate_disease weighted_rate adj_rate) pop_est=tot_pop);

   /* Define how to display the variables */
   define region / group 'Region' style=[font_weight=bold];
   define HSDA / group 'HSDA' style=[font_weight=bold];
   define disease_type / group 'Disease Type' style=[font_weight=bold];
   define yr_cal / across 'Calendar year';
   define age_grp / across 'Age group';
   define sex / across 'Sex';
   define pop_est / analysis sum 'N' format=8.3;
   define rate_disease / 'Disease rate' sum;
   define weighted_rate / 'Weight rate' sum;
   define adj_rate / computed 'Adjusted Rate' format=8.2;
   define tot_pop / analysis sum 'Total Year';

   /* Compute the adjusted rate using a loop over columns */
   compute adj_rate;
      /* Requires more thinking */
      array vars{*} _c1-_c77_ temporary;
      do i = 7 to 77 by 4;
         col1 = vars&i-1.;
         col2 = vars&i-2.;
         col3 = col1*col2;
         call symputx(catx('_c',i),col3);
      end;
   endcomp;

   /* Calculation by hand */
   _c7_ = _c5_ * _c6_;
   _c11_ = _c9_ * _c10_;
   _c15_ = _c13_ * _c14_;
   _c19_ = _c17_ * _c18_;
   _c23_ = _c21_ * _c22_;
   _c27_ = _c25_ * _c26_;
   _c31_ = _c29_ * _c30_;
   _c35_ = _c33_ * _c34_;
   _c39_ = _c37_ * _c38_;
   _c43_ = _c41_ * _c42_;
   _c47_ = _c45_ * _c46_;
   _c51_ = _c49_ * _c50_;
   _c55_ = _c53_ * _c54_;
   _c59_ = _c57_ * _c58_;
   _c63_ = _c61_ * _c62_;
   _c67_ = _c65_ * _c66_;
   _c71_ = _c69_ * _c70_;
   _c75_ = _c73_ * _c74_;

   break after Region/summarize skip ol;
   break after / summarize skip ol;
run;


						
						
	
	