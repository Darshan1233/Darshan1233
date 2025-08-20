[![@darshan1233's Holopin board](https://holopin.io/api/user/board?user=darshan1233)](https://holopin.io/@darshan1233)


### Hi there Myself Darshan Deshmukh👋

<!--
**Darshan1233/Darshan1233** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on Wallycon
- 🌱 I’m currently learning C++, Java, Python
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: he/him
- ⚡ Fun fact: ...
-->


import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { UserService } from '../../services/user.service';
import { ToastrService } from 'ngx-toastr';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  templateUrl: './user-form.component.html'
})
export class UserFormComponent implements OnInit {
  userForm!: FormGroup;
  id!: number;
  isEdit = false;

  constructor(
    private fb: FormBuilder,
    private route: ActivatedRoute,
    private router: Router,
    private userService: UserService,
    private toastr: ToastrService
  ) {}

  ngOnInit() {
    this.userForm = this.fb.group({
      firstName: ['', [Validators.required, Validators.minLength(2), Validators.maxLength(15)]],
      lastName: ['', [Validators.required, Validators.minLength(2), Validators.maxLength(15)]],
      email: ['', [Validators.required, Validators.email]],
      password: ['', [Validators.minLength(6), Validators.maxLength(16)]],
      confirmPassword: ['']
    });

    this.id = this.route.snapshot.params['id'];
    if (this.id) {
      this.isEdit = true;
      this.userService.getUser(this.id).subscribe((data) => {
        this.userForm.patchValue(data);
      });
    }
  }

  saveUser() {
    if (this.userForm.invalid) return;

    const user = this.userForm.value;
    if (!this.isEdit && user.password !== user.confirmPassword) {
      this.toastr.error("Passwords don't match");
      return;
    }

    if (this.isEdit) {
      this.userService.updateUser(this.id, user).subscribe(() => {
        this.toastr.success('User updated successfully');
        this.router.navigate(['/users']);
      });
    } else {
      this.userService.addUser(user).subscribe(() => {
        this.toastr.success('User added successfully');
        this.router.navigate(['/users']);
      });
    }
  }
}