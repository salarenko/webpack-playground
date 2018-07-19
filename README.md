# webpack-playground

import {HttpEvent, HttpHandler, HttpHeaders, HttpInterceptor, HttpRequest} from '@angular/common/http';
import {Injectable, Injector} from '@angular/core';
import {Observable} from 'rxjs/Observable';
import {OAuthService} from 'angular-oauth2-oidc';

@Injectable()
export class AuthenticationInterceptor implements HttpInterceptor {

  constructor(private _injector: Injector) {
  }

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const oauthService = this._injector.get(OAuthService);
    let httpHeaders: HttpHeaders = req.headers;

    if (oauthService.hasValidAccessToken()) {
      httpHeaders = httpHeaders.append('Authorization', 'Bearer ' + oauthService.getAccessToken());
    }
    if (!httpHeaders.has('Content-Type') && (!req.url.includes('attachments') || req.method !== 'POST')) {
      httpHeaders = httpHeaders.append('Content-Type', 'application/json');
    }

    return next.handle(req.clone({
      headers: httpHeaders
    }));
  }
}
