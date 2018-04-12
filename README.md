TASK DATE (MEDIUM - middle level): 12.04.2018 - FINISHED: 12.03.2017


TASK SHORT DESCRIPTION: 1392_2nd_version [
								Option to hide the heart, and/or donate button, and/or donors list 
								from the campaign boxes on the Support Us page 
								
								this is the difference: individually
							]

							
GITHUB REPOSITORY CODE: feature/task-1392-options-to-hide-on-support-us-page-individually


ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/feature/task-1392-options-to-hide-on-support-us-page-individually


ADDED NEW FILES

	\network-site\assets\_commons\common_fns.js
	\network-site\addons\default\modules\network_settings\language\english\support_us_lang.php
	\network-site\addons\default\modules\network_settings\views\fundraising\partials\support_us_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_hearts.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_donors_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_hearts_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_hearts_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_hearts_no_donors_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_hearts.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_donors_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_hearts_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_hearts_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_no_values_no_hearts_no_donors_no_buttons.php	
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_hearts.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_donors_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_hearts_no_buttons.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_hearts_no_donors.php
	\network-site\addons\default\modules\fundraising\views\partials\campaign_single_no_hearts_no_donors_no_buttons.php

CHANGES
 
	IN FILES: 
	
		\network-site\addons\default\themes\toucantechV2\css\supportus.css
		
			ADDED CODE: 
			
				.target-total {
				  margin-top: 10px;
				  font-weight: 700;
				   margin-bottom: 10px;
				}

				

		\network-site\addons\default\modules\fundraising\controllers\fundraising.php
	
			ADDED CODE inside function supportus
			
				.....
				//Determine which campaign partial view will be used
				$viewFileName = "";
				if ( ! $campaign->display_target_values ) $viewFileName .= "_no_values";
				if ( ! $campaign->display_hearts ) $viewFileName .= "_no_hearts";
				if ( ! $campaign->display_donors_list or $campaign->donorsNb == 0 ) $viewFileName .= "_no_donors";
				if ( ! $campaign->display_donate_buttons ) $viewFileName .= "_no_buttons";
				$campaign->view_file_name = $viewFileName;
				.....
				
	
		\network-site\addons\default\modules\fundraising\views\supportus.php
	
			CHANGED CODE: 
			
				I.  (in two places) 
				
					FROM: <?php $this->load->view('partials/campaign', array('campaign1'=>$campaign1))?>
					TO:   <?php $this->load->view('partials/campaign' . $campaign1->view_file_name, array('campaign1'=>$campaign1))?>
				
				II. TO: <?php $this->load->view('partials/campaign_single' . $campaign->view_file_name, array('campaign' => $campaign))?>

	
		\network-site\addons\default\modules\network_settings\views\fundraising\campaigns\edit.php
		
			ADDED CODE:
				
				....
					<div style="clear: both;"></div>
					<?=$buttons['hearts']?>
					<?=$buttons['donateButtons']?>
					<?=$buttons['donorsList']?>
					<?=$buttons['targetValues']?>
				.... 
				
				other ... to show modal
			
					<!-- BEGINNING MODAL -->
					<div class="modal fade" id="myModal" role="dialog">
						<div class="modal-dialog">
							<!-- Modal content-->
							<div class="modal-content">
								<div class="modal-header">
									<button type="button" class="close" data-dismiss="modal">&times;</button>
									<h4 class="modal-title" id="modal_block_header"></h4>
								</div>
								<div class="modal-body">
									<p id="modal_block_message"></p>
								</div>
								<div class="modal-footer">
									<button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
								</div>
							</div>
						</div>
					</div><!-- END MODAL -->
				
				........
			
			
			
			
		\network-site\addons\default\modules\network_settings\js\fundraising.js

			ADDED CODE: new controll part 
			
				//Easy control for buttons to toggle "Hearts", "Donate Buttons" and "Donors list"
				if ($('.toggle-display-on-support-us-page')[0]) {
					$('.toggle-display-on-support-us-page').live('click', function(event) 
					{
						//avoiding multiple firing
						event.preventDefault();

						//set necessary stuffs
						var $this = $(this);
						var thisId = $this.prop('id');
						var btnSelector = '#' + thisId;
						var value = $this.prop('value');
						var slug = thisId.replace('btn_', '');

						//disable button
						COMMON.disableButton(btnSelector);
						
						//Set value in DB with AJAX
						AJAX.call('network_settings/content/ajax_support_us_toggle', {'slug' : slug, 'value' : value}, function(response) {
							var res = $.parseJSON(response);
							if (res['result'] == true) {
								$this.html(res['btnLabel']);
								$this.parent().find('span.status-label').html(res['btnStatus']);
								if (res['value'] == '1') {
									COMMON.enableButton(btnSelector, 'btn-warning');
								} else {
									COMMON.enableButton(btnSelector, 'btn-success');
								}
								$this.val(res['value']);
							}

							//displaying result of updating
							$('#modal_block_header').html(res['header']);
							$('#modal_block_message').html(res['msg']);
							$('#myModal').modal('toggle');
						}) 
					})
				} //END Easy control for buttons to toggle "Hearts", "Donate Buttons" and "Donors list"
				
	
		\network-site\addons\default\modules\network_settings\controllers\fundraising.php
		
			ADDED CODE 
			
				A new function 
				
					//Toggling buttons - set value of "Hearts", "Donors list" and "Donate buttons" value in default_settings table
					public function ajax_support_us_toggle() 
					{
						if ( ! $this->input->is_ajax_request() ) exit;
						
						$this->lang->load('network_settings/support_us');
						
						$slug = trim($this->input->post('slug'), " \r\n");
						
						$value = ( (string)$this->input->post('value') == '1' ) ? '0' : '1';
						
						$result = $this->db->set('value', $value)->where('slug', $slug)->update('settings');

						$switchEnabled = ($value == '1') ? lang('support_us:label:enabled') : lang('support_us:label:disabled');
						$section = str_replace('_', ' ', str_replace('support_us_', '', $slug));
						
						echo json_encode(array(
							'result' => $result,
							'value' => $value,
							'btnLabel' => ($value == '1') ? lang('support_us:label:disable') : lang('support_us:label:enable'),
							'btnStatus' => $switchEnabled,
							'header' => sprintf(lang('support_us:title:update_result'), $section),
							'msg' => sprintf(lang('support_us:msg:update_result'), ucfirst($section), $switchEnabled),
						));
					} //END function ajax_support_us_toggle
		
				Inside function editcampaign
				
					//Create html snippets for buttons which toggling "hearts", "donors list" and "donate buttons" on public support us page
					# NOTE : JS control for this buttons can be found here: network_settings\js\fundraising.js
					$buttons = array();
					$this->lang->load('network_settings/support_us');
					$enabled = lang('support_us:label:enabled'); 
					$disabled = lang('support_us:label:disabled'); 
					$enable = lang('support_us:label:enable'); 
					$disable = lang('support_us:label:disable'); 
					#Get button for "Hearts" toggling on (public) support us page 
					$buttonParams = ( $campaign->display_hearts ) 
											? array('value' => '1', 'btnStyle' => 'btn-warning', 'btnLabel' => $disable, 'typeId' => 'hearts', 'type' => 'hearts', 'btnStatus' => $enabled)
											: array('value' => '0', 'btnStyle' => 'btn-success', 'btnLabel' => $enable, 'typeId' => 'hearts', 'type' => 'hearts', 'btnStatus' => $disabled);
					$buttons['hearts'] = $this->load->view('content/partials/support_us_buttons', $buttonParams, true);
					#Get button for "Donate buttons" toggling on (public) support us page 
					$buttonParams = ( $campaign->display_buttons ) 
											? array('value' => '1', 'btnStyle' => 'btn-warning', 'btnLabel' => $disable, 'typeId' => 'donate_buttons', 'type' => 'donate buttons', 'btnStatus' => $enabled)
											: array('value' => '0', 'btnStyle' => 'btn-success', 'btnLabel' => $enable, 'typeId' => 'donate_buttons', 'type' => 'donate buttons', 'btnStatus' => $disabled);
					$buttons['donateButtons'] = $this->load->view('content/partials/support_us_buttons', $buttonParams, true);
					#Get button for "Donors list" toggling on (public) support us page 
					$buttonParams = ( $campaign->display_donors ) 
											? array('value' => '1', 'btnStyle' => 'btn-warning', 'btnLabel' => $disable, 'typeId' => 'donors_list', 'type' => 'donors list', 'btnStatus' => $enabled)
											: array('value' => '0', 'btnStyle' => 'btn-success', 'btnLabel' => $enable, 'typeId' => 'donors_list', 'type' => 'donors list', 'btnStatus' => $disabled);
					$buttons['donorsList'] = $this->load->view('content/partials/support_us_buttons', $buttonParams, true);
					#Get button for "Target values" toggling on (public) support us page 
					$buttonParams = ( $campaign->display_values ) 
											? array('value' => '1', 'btnStyle' => 'btn-warning', 'btnLabel' => $disable, 'typeId' => 'target_values', 'type' => 'target values', 'btnStatus' => $enabled)
											: array('value' => '0', 'btnStyle' => 'btn-success', 'btnLabel' => $enable, 'typeId' => 'target_values', 'type' => 'target values', 'btnStatus' => $disabled);
					$buttons['targetValues'] = $this->load->view('content/partials/support_us_buttons', $buttonParams, true);
					..... 
					
					->set('buttons', $buttons)
					
					.....
					
				
	
		\network-site\addons\default\modules\network_settings\details.php
		
			ADDED CODE: 
				
				if (version_compare($old_version, '2.0.95', 'lt')) {
					$this->db->add_boolean_field_to_table($this->db->dbprefix('fundraising_campaigns'), $field = 'display_hearts', $null = false, $default = true, $after = '');
					$this->db->add_boolean_field_to_table($this->db->dbprefix('fundraising_campaigns'), $field = 'display_donate_buttons', $null = false, $default = true, $after = '');
					$this->db->add_boolean_field_to_table($this->db->dbprefix('fundraising_campaigns'), $field = 'display_donors_list', $null = false, $default = true, $after = '');
					$this->db->add_boolean_field_to_table($this->db->dbprefix('fundraising_campaigns'), $field = 'display_target_values', $null = false, $default = true, $after = '');			
					#delete from settings table the records which were inserted with earlier version
					$this->db->where('slug', 'support_us_target_values')
								->or_where('slug', 'support_us_hearts')
								->or_where('slug', 'support_us_donors_list')
								->or_where('slug', 'support_us_donate_buttons')
								->delete($this->db->dbprefix('settings'));
				}
				
